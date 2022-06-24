# Benchmark

I'm using [plow](https://github.com/six-ddc/plow) to benchmark 3 frameworks written in Go / PHP and NodeJS, the concurrency shall be 1000 as my macbook can only handle at least 1000, while we need to reach out 100000 requests

Label | [Lucidfy](https://github.com/lucidfy/lucid) | [Laravel](https://github.com/laravel/laravel) | [NestJS](https://github.com/nestjs/nest)
---|---|---|---
Elapsed | 12.925s | 3m12.994s | 17.694s
Http 2xx | 99477 | 15341 | 83317
Success Rate | 99.47% | 15.34% | 83.31%
RPS | 7736.802 | 518.148 | 5651.373
Reads | 12.243MB/s | 0.087MB/s | 1.105MB/s
Writes | 0.427MB/s | 0.057MB/s | 0.286MB/s

**Disclaimer:** the benchmark above differs based on machine specification, here's the spec of the machine I used.

```sh
MacBook Pro (13-inch, 2020, Four Thunderbolt 3 ports)
2.3 GHz Quad-Core Intel Core i7
16 GB 3733 MHz LPDDR4X
```

## Frameworks

GO - https://github.com/lucidfy/lucid

```sh
git clone git@github.com:lucidfy/lucid.git
go mod download
./build
./.bin/lucid

plow http://0.0.0.0:8080 -c 1000 -n 100000

Summary:
  Elapsed     12.925s
  Count        100000
    2xx         99477
  RPS        7736.802
  Reads    12.243MB/s
  Writes    0.427MB/s

Error:
  59   "dial tcp4 0.0.0.0:8080: connect: connection reset by peer"
  464  "the server closed connection before returning the first response byte. Make sure the server returns 'Connection: close' response header before closing the connection"       P99       P99.9

Statistics    Min      Mean      StdDev       Max
  Latency    452µs   128.462ms  135.049ms  1.443769s
  RPS       6191.32   7707.43     501.1     8042.41

Latency Percentile:
  P50         P75        P90        P95       P99       P99.9     P99.99  37ms  11101  ■■■■■■■
  98.79ms  196.182ms  316.579ms  394.868ms  585.08ms  841.425ms  1.044457s09ms    462

Latency Histogram:
  65.214ms   68284  68.28%
  182.461ms  17703  17.70%
  334.827ms  11452  11.45%
  472.27ms    1895   1.90%
  596.227ms    467   0.47%
  757.928ms    179   0.18%
  953.68ms      18   0.02%
  1.147061s      2   0.00%
```

---

PHP - https://github.com/laravel/laravel - Laravel Framework 8.83.12 under PHP 7.4.15

**Disclaimer:** I can't install php 8 in my local machine as I need to support some php 7 projects as of the moment, hopefully someone can benchmark a 1000 concurrent connection in php8

```sh
laravel new laravel
cd laravel/
composer install
# replace the web/routes.php and put "Hello from Laravel!"
# Route::get('/', function () {
#    return 'Hello from Laravel!';
#    // return view('welcome');
# });
php artisan config:cache
php artisan route:cache
php artisan serve --host=0.0.0.0

plow http://0.0.0.0:8000 -c 1000 -n 100000

Summary:
  Elapsed  3m12.994s
  Count       100000
    2xx        15341
  RPS        518.148
  Reads    0.087MB/s
  Writes   0.057MB/s

Error:
  5604   "dial tcp4 0.0.0.0:8000: connect: connection reset by peer"pipe"
  4862   "dial tcp4 0.0.0.0:8000: i/o timeout"
  42139  "dialing to the given TCP address timed out"
  31998  "the server closed connection before returning the first response byte. Make sure the server returns 'Connection: close' response header before closing the connection"

Statistics   Min     Mean      StdDev      Max
  Latency   243µs  1.906595s  1.27975s  7.941495s
  RPS        56     521.15     426.16    2224.04

Latency Percentile:
  P50           P75        P90        P95        P99       P99.9     P99.99
  2.057554s  3.000442s  3.102668s  3.402525s  4.189429s  5.349961s  7.109489s

Latency Histogram:
  399.618ms  35278  35.28%
  1.386836s   8613   8.61%
  2.907642s  52579  52.58%
  3.134087s   2841   2.84%
  3.95783s     589   0.59%
  4.739884s     41   0.04%
  5.047551s     53   0.05%
  6.506075s      6   0.01%
```

---

NodeJS - https://github.com/nestjs/nest - NestJS "@nestjs/cli": "^8.1.3"

```sh
git clone https://github.com/nestjs/typescript-starter.git nestjs
cd nestjs
npm install
npm run build
npm run start:prod

plow http://0.0.0.0:3000 -c 1000 -n 100000

Summary:
  Elapsed    17.694s
  Count       100000
    2xx        83317
  RPS       5651.373
  Reads    1.105MB/s
  Writes   0.286MB/s

Error:
  14775  "dial tcp4 0.0.0.0:3000: connect: connection refused"
  1908   "the server closed connection before returning the first response byte. Make sure the server returns 'Connection: close' response header before closing the connection"

Statistics   Min      Mean      StdDev       Max
  Latency    82µs   175.988ms  157.589ms  2.096533s
  RPS       964.01   5637.32    2714.91   14761.18

Latency Percentile:
  P50           P75        P90        P95        P99       P99.9     P99.99  s   1526  ■
  166.519ms  190.506ms  225.011ms  258.363ms  1.307728s  1.681013s  1.890436ss  34734  ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

Latency Histogram:
  18.403ms   13945  13.95%
  128.722ms   1526   1.53%
  164.691ms  42738  42.74%
  197.535ms  34840  34.84%
  422.503ms   6728   6.73%
  1.623889s    133   0.13%
  1.84722s      89   0.09%
  2.071929s      1   0.00%
```
