# Bypass Url Parser

Tool that tests `MANY` url bypasses to reach a `40X protected page`.

If you wonder why this code is `nothing but a dirty curl wrapper`, here's why:

- Most of the python requests do url/path/parameter encoding/decoding, and I hate this.
- If I submit raw chars, I want raw chars to be sent.
- If I send a weird path, I want it weird, not normalized.

This is `surprisingly hard` to achieve in python without loosing all of the lib goodies like parsing, ssl/tls encapsulation and so on. \
So, be like me, use `curl as a backend`, it's gonna be just fine.

Also, this tool can be used as a library, see [lib_sample_usage.py](lib_sample_usage.py)

## Usage

```
Bypass Url Parser, made with love by @TheLaluka
A tool that tests MANY url bypasses to reach a 40X protected page.

Usage:
    ./bypass_url_parser.py (-u <URL> | -R <file>) [-m <mode>] [-o <outdir>] [-S <level>] [ (-H <header>)...] [-r <num>]
                           [-s <ip>] [--spoofip-replace] [-p <port>] [--spoofport-replace] [--request-tls]
                           [--dump-payloads] [-t <threads>] [-T <timeout>] [-x <proxy_url>] [-v | -d | -dd]

Program options:
    -u, --url <URL>           URL (path is optional) to run bypasses against
    -R, --request <file>      Load HTTP raw request from a file
    -H, --header <header>     Header(s) to use, format: "Cookie: can_i_haz=fire"
    -m, --mode <mode>         Bypass modes. See 'Bypasser.BYPASS_MODES' in code [Default: all]
    -o, --outdir <outdir>     Output directory for results
    -x, --proxy <proxy_url>   Set a proxy in the format http://proxy_ip:port.
    -S, --save-level <level>  Save results level. From 0 (DISABLE) to 3 (FULL) [Default: 1]
    -s, --spoofip <ip>        IP(s) to inject in ip-specific headers
    -p, --spoofport <port>    Port(s) to inject in port-specific headers
    -r, --retry <num>         Retry attempts of failed requests. Set 0 to disable all retry tentatives [Default: 3]
    -t, --threads <threads>   Scan with N parallel threads [Default: 1]
    -T, --timeout <timeout>   Request times out after N seconds [Default: 5]

General options:
    -h, --help                Show help, you are here :)
    -v, --verbose             Verbose output
    -d, --debug               Show more details like curl commands generated by this tool
    -dd, --debug              Print Debug level 2 (with all classes debug_class output)
    -V, --version             Show version info

Misc options:
    --spoofip-replace         Disable list of default internal IPs in 'http_headers_ip' bypass mode
    --spoofport-replace       Disable list of default internal ports in 'http_headers_port' bypass mode
    --request-tls             Force usage of TLS/HTTPS for the request load with the '-R, --request' option
    --dump-payloads           Print all payloads (curls) generated by this tool.

Examples:
    ./bypass_url_parser.py -u "http://127.0.0.1/juicy_403_endpoint/" -s 8.8.8.8 -d
    ./bypass_url_parser.py -u /path/urls -t 30 -T 5 -H "Cookie: me_iz=admin" -H "User-agent: test"
    ./bypass_url_parser.py -R /path/request_file --request-tls -m "mid_paths, end_paths"
```

## Expected result

```bash
./bypass_url_parser.py -u http://127.0.0.1:8000/foo/bar
2022-08-09 14:52:40 lalu-perso bup[361559] WARNING Trying to bypass 'http://127.0.0.1:8000/foo/bar' url (3213 payloads)...
2022-08-09 14:52:40 lalu-perso bup[361559] INFO Doing: 50 / 3213
[...]
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Doing: 3200 / 3213
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Retry (1/3) the '16' failed curl commands with 10 threads and 10s timeout
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Retry (2/3) the '16' failed curl commands with 5 threads and 20s timeout
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Retry (3/3) the '16' failed curl commands with 1 threads and 30s timeout
2022-08-09 14:52:55 lalu-perso bup[361559] INFO
[#####] [bypass_method] [payload] => [status_code] [content_type] [content_length] [lines_count] [word_counts] [title] [server] [redirect_url]
[GROUP (1587)] [original_request] [http://127.0.0.1:8000/foo/bar] => [404] [text/html] [469] [14] [95] [Error response] [SimpleHTTP/0.6 Python/3.8.10] []
[GROUP (10)] [http_methods] [-X CONNECT http://127.0.0.1:8000/foo/bar] => [501] [text/html] [500] [14] [96] [Error response] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000/???foo/bar] => [200] [text/html] [913] [26] [27] [Directory listing for /???foo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000//???foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/???foo/bar]
[SINGLE] [mid_paths] [http://127.0.0.1:8000/??foo/bar] => [200] [text/html] [911] [26] [27] [Directory listing for /??foo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000//??foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/??foo/bar]
[SINGLE] [mid_paths] [http://127.0.0.1:8000/?foo/bar] => [200] [text/html] [909] [26] [27] [Directory listing for /?foo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000//?foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/?foo/bar]
[SINGLE] [mid_paths] [http://127.0.0.1:8000///?anythingfoo/bar] => [200] [text/html] [929] [26] [27] [Directory listing for ///?anythingfoo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000////?anythingfoo/bar] => [200] [text/html] [931] [26] [27] [Directory listing for ////?anythingfoo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[GROUP (2)] [mid_paths] [http://127.0.0.1:8000/#?foo/bar] => [200] [text/html] [893] [26] [27] [Directory listing for /] [SimpleHTTP/0.6 Python/3.8.10] []
[GROUP (2)] [mid_paths] [http://127.0.0.1:8000//#?foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/]
```

## Setup

### LINUX

```bash
# Deps
sudo apt install -y bat curl virtualenv python3
# Tool
virtualenv -p python3 .py3
source .py3/bin/activate
pip install -r requirements.txt
./bypass_url_parser.py -u http://thinkloveshare.com/juicy_403_endpoint/
cat /tmp/tmpRANDOM-bypass-url-parser/triaged-bypass.json  | jq -r '.results[].request_curl_cmd'
cat /tmp/tmpRANDOM-bypass-url-parser/triaged-bypass.json  | jq -r '.results[].response_data'
```

### DOCKER

```bash
docker build -t thelaluka/bypass-url-parser:latest .
docker run --rm -it --net=host -v "$PWD:/host" thelaluka/bypass-url-parser:latest -u http://127.0.0.1:8000/dummy
docker run --rm -it --net=host -v "$PWD:/host" thelaluka/bypass-url-parser:latest -u /host/urls.lst
# TODO later, tag & push releases to dockerhub :)
# -t thelaluka/bypass-url-parser:0.2.0
# docker push thelaluka/bypass-url-parser:latest
```

## More about supported arguments

### Arguments parsing

Bypass_url_parser allows to define some arguments in many ways:

- `-m, --mode`, `-s, --spoofip` and `-p, --spoofport` arguments can be a filename, a string, a comma-separated string list or a list (when `Bypasser` is used as a library);
- `-u, --url` argument can be a filename, a string or a list (when `Bypasser` is used as a library);
- `stdin` (with `-`) is supported for all these arguments.

For example, if you want to define several target urls (`-u, --url`), all the following commands produce the same result:

```bash
./bypass_url_parser.py -u http://thinkloveshare.com/test
./bypass_url_parser.py -u /path/urls
cat /path/urls | ./bypass_url_parser.py -u -
echo 'http://thinkloveshare.com/test' | ./bypass_url_parser.py -u -
```

### Target definition

A target must be defined for the tool to work. 2 options:
- `-u, --url`: URL(s), in GET
- `-R, --request`: Request file. The protocol can't be guessed from file, so `http` by default or `https` if `--request-tls` option is present.

### Bypass mode

If `-m, --mode` is specified, you can select the desired bypass mode to run a specific test (or tests) and reduce the number of requests sent by the tool.

For now, the following bypass mode(s) are supported:

```
all, mid_paths, end_paths, case_substitution, char_encode, http_methods, http_versions, http_headers_method, http_headers_scheme, http_headers_ip, http_headers_port, http_headers_url
```

Example:

```bash
./bypass_url_parser.py -u /path/urls -m "case_substitution, char_encode, http_headers_scheme"
```

### Spoofip / Spoofport

In order to customize the ip addresses and ports used in bypass attempts, the tool supports the following options:

- With `-s, --spoofip` you can set some IP(s) to inject into `ip-specific` headers (`X-Forwarded-For`, `X-Real-Ip`, etc.)
- With `-p, --spoofport` you can set some ports to inject into `port-specific` headers (`X-Forwarded-Port`)

By default, these custom entries are added to the internal IP/port lists. If you want to use only your IP(s)/port(s), you can use `--spoofip-replace` and/or `--spoofport-replace` arguments.

Example:

```bash
./bypass_url_parser.py -u /path/urls -s /path/custom_ip --spoofip-replace
./bypass_url_parser.py -u /path/urls -p "3000, 9443, 10443"
```

### Results saving

By default, if target url is unique, the tool saves a copy of the results in `/tmp/tmpXXX-bypass-url-parser/triaged-bypass.log` log file.

***Notes:** If multiple target urls are passed to `-u`, results are prefixed with the url as directory (`/tmp/tmpXXX-bypass-url-parser/http-target-com-8080-api-users/`).*

There are two arguments to customize this behavior:

- `-o, --outdir` to set a custom output directory
- `-S, --save-level` to choose a saving level

The saving levels are:

- `0` (NONE): Disable output saving and output directory creation;
- `1` (MINIMAL): Only save the program log file which contains the results (Default);
- `2` (PERTINENT): Save the program log file and pertinent (results) curl responses in separate html files;
- `3` (FULL): Save the program log file and all curl responses in separate html files.

Example:

```bash
./bypass_url_parser.py -S 0
./bypass_url_parser.py -o /tmp/bypass-res
./bypass_url_parser.py -o /tmp/bypass-res2 -S 2 -u http://thinkloveshare.com/juicy_403_endpoint/
tree /tmp/bypass-res2/
├── bypass-14193b8d2e14ec60d52405f46c5d35f3.html
├── bypass-782550777a661b7bef046b5d899b403a.html
├── bypass-e06268bcef8506053feed2646af4e773.html
├── bypass-ebdf6466c5ef82ab08a7b97324662bf0.html
└── triaged-bypass.log

0 directories, 5 files
```

## Non-Regression tests & Code Cleanup

```bash
# Code Cleanup
pre-commit run --all-files
# Ensure no regression is pushed
./bypass_url_parser.py -S 0 -v -u http://127.0.0.1:8000/foo/bar --dump-payloads > "tests-history/bup-payloads-$(date +'%Y-%m-%d').lst"
# Compare /tmp/bup-payloads-YYYY-MM-DD.lst and the latest tests-history/bup-payloads-YYYY-MM-DD.lst
git diff --no-index tests-history/bup-payloads-[OLD].lst tests-history/bup-payloads-[NEW].lst
# TODO create ls/sort/diff bash command for maintainers
git commit -m "My cool feature or bugfix"
git tag -a vX.Y.Z "$COMMIT_HASH" -m "New release: vX.Y.Z"
git push --tags
# If X or Y is bumped, create new release on github
```

## Contributors

- Initial release by [@TheLaluka](https://twitter.com/TheLaluka)
- Huge refactoring & lib-mode with thanks to [@jtop_fap](https://twitter.com/jtop_fap)