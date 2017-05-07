# SWA Dashboard
Dashboard to monitor and receive alerts for changes in Southwest fare prices.

![image](https://cloud.githubusercontent.com/assets/6979737/17744714/99f15da2-646e-11e6-8f13-60c716f1e865.png)

## Why?
I'm a lazy programmer who was tired of checking flight prices. ¯\\\_(ツ)\_/¯

## Installation
Since I would rather not get in trouble for publishing this tool to npm, you can
clone the repo locally and use `npm link` to use the executable.
```
cd wherever-you-cloned-it-to
npm link
```

## Usage
It will scrape Southwest's prices every `n` minutes (`n` = whatever interval you
define via the `--interval` flag) and compare the results, letting you know the
difference in price since the last interval. The default interval is 30 mins and
the default fare type is dollars.

You may optionally set the `--individual-deal-price` flag, which will alert you
if either fare price falls below the threshold you define. There is also the
optional `--total-deal-price` flag, which will alert you if the combined total
of both fares falls below the threshold. Most flags are required, unless stated
otherwise.

```bash
swa \
  --from 'DAL' \
  --to 'LGA' \
  --leave-date '11/01/2017' \
  --return-date '11/08/2017' \
  --leave-time 'anytime' \ # Can be anytime, morning, afternoon, evening (optional)
  --return-time 'anytime' \ # Can be anytime, morning, afternoon, evening (optional)
  --fare-type 'dollars' \ # Can be dollars or points (optional)
  --passengers 2 \
  --individual-deal-price 50 \ # In dollars or points (optional)
  --total-deal-price 120 \ # In dollars or points (optional)
  --interval 5 \ # In minutes (optional)
  --daily-update \ # Send a daily SMS update of prices (optional)
  --daily-update-at '19:00' \ # Time to send daily update, default is 18:00/6pm (optional)
  --nonstop \ # Filter to only nonstop flights (optional)
```

If you would like to look at flights going **one way** between two airports, you can use the `--one-way` flag. The optional `--specific-flight` flag will limit the search to a specific flight number. This ignores values entered with `--return-date` and `--return-time`, and `--total-deal-price`.

```bash
swa \
  --one-way
  --from 'DAL' \
  --to 'LGA' \
  --leave-date '11/01/2017' \
  --leave-time 'anytime' \ # Can be anytime, morning, afternoon, evening (optional)
  --fare-type 'dollars' \ # Can be dollars or points (optional)
  --passengers 2 \
  --individual-deal-price 50 \ # In dollars or points (optional)
  --interval 5 # In minutes (optional)
  --specific-flight 1234 # Only show price for a specific flight number (optional)
```

### Twilio integration
If you have a Twilio account (I'm using a free trial account) and you've set up
a deal price threshold, you can set the following environment vars to set up SMS
deal alerts. _Just be warned: as long as the deal threshold is met, you're going
to receive SMS messages at the rate of the interval you defined. Better wake up
and book those tickets!_

When the Twilio environment variables are configured, you can also use the daily
update flags `--daily-update` and `--daily-update-at` to send you a daily SMS
message with the current fare prices. The `--daily-update-at` flag is in 24
hour time format.

```bash
export TWILIO_ACCOUNT_SID=""
export TWILIO_AUTH_TOKEN=""
export TWILIO_PHONE_FROM=""
export TWILIO_PHONE_TO=""
```

## Troubleshooting

### Python 2 requirement
When building the app, the sub module node-gyp has a dependency on Python 2 at this time.

### Node >=5.11.0 requirement
If you receive a ``SyntaxError: Unexpected token ...`` upon running the `swa`
command, make sure you are running a version of node that supports ES6
syntax (5.11.0 and up).

### C++11 compiler requirement
You may experience compilation errors when you attempt to run `npm link`.  If so,
you'll need to make sure you have a C++11 compiler installed on your system.
If you're running on Windows this is sometimes resolved by installing the [Visual C++
Build Tools](http://landinghub.visualstudio.com/visual-cpp-build-tools).  For \*nix
systems you'll need to find the specific package needed for your particular OS.

### libxmljs requirement
Under some circumstances, libxmljs may throw an error that looks like this:

```
Error: Could not locate the bindings file. Tried:
 → /root/swa-dashboard/node_modules/libxmljs/build/xmljs.node
```

You can fix it by rebuilding libxmljs manually:

```
sudo npm install -g node-gyp
cd node_modules/libxmljs
node-gyp rebuild
```
