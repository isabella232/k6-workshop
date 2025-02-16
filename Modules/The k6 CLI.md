# The k6 CLI

Once you have a k6 script, you can use the k6 Command Line Interface (CLI) to interact with it from your terminal. The k6 CLI allows you to execute k6 test scripts and change execution settings using a system of commands and flags.

Here are the three most common commands.

| Command   | Description                    | Usage            |
| --------- | ------------------------------ | ---------------- |
| `help`    | Displays all possible commands | `k6 help`        |
| `run`     | Executes a k6 script           | `k6 run test.js` |
| `version` | Displays installed k6 version  | `k6 version`                 |

Flags are settings that are added to commands to change a part of the configuration. Below is an overview of common flags for the command `run`:


| Flag                   | Description                                                    | Usage                                    |
| ---------------------- | -------------------------------------------------------------- | ---------------------------------------- |
| `--help`               | Displays possible flags for the given command                  | `k6 run --help`                          |
| `--vus` or `-u`        | Sets number of virtual users                                   | `k6 run test.js --vus 10 --duration 30s` |
| `--duration`           | Sets the duration of the test                                  | `k6 run test.js --duration 10m`          |
| `--iterations` or `-i` | Instructs k6 to iterate the default function a number of times | `k6 run test.js -i 3`                    |
| `-e`                   | Sets an environment variable to pass to the script             | `k6 run test.js -e DOMAIN=test.k6.io`                                         |

In this section, you'll learn about these common commands and flags.

## Help

Executing `k6 help` will show you a list of all the available commands. 

```shell
          /\      |‾‾| /‾‾/   /‾‾/
     /\  /  \     |  |/  /   /  /
    /  \/    \    |     (   /   ‾‾\
   /          \   |  |\  \ |  (‾)  |
  / __________ \  |__| \__\ \_____/ .io

Usage:
  k6 [command]

Available Commands:
  archive     Create an archive
  cloud       Run a test on the cloud
  convert     Convert a HAR file to a k6 script
  help        Help about any command
  inspect     Inspect a script or archive
  login       Authenticate with a service
  pause       Pause a running test
  resume      Resume a paused test
  run         Start a load test
  scale       Scale a running test
  stats       Show test metrics
  status      Show test status
  version     Show application version

Flags:
  -a, --address string      address for the api server (default "localhost:6565")
  -c, --config string       JSON config file (default "/Users/nic/Library/Application Support/loadimpact/k6/config.json")
  -h, --help                help for k6
      --log-output string   change the output for k6 logs, possible values are stderr,stdout,none,loki[=host:port] (default "stderr")
      --logformat string    log output format
      --no-color            disable colored output
  -q, --quiet               disable progress updates
  -v, --verbose             enable verbose logging

Use "k6 [command] --help" for more information about a command.
```

To get even more information about a particular command, you can add the `--help` flag, like this:

```shell
k6 run --help
```


## Execution and Execution Options: `run`

Another common k6 command is `k6 run [filename].js`. In the previous section, you learned how to use `k6 run` to execute an existing k6 script in JavaScript. 

Without any modification, `k6 run` instructs k6 to run your script as it is (see [Changing settings in k6](The%20k6%20CLI.md#Changing%20settings%20in%20k6) to understand how k6 determines which configuration settings to use). However, you can also use flags with the `run` command to override settings within the script (such as [k6 Load Test Options](k6%20Load%20Test%20Options.md)) from the command line.

### Setting test parameters for `run` with flags

#### Duration

The duration specifies how long the test will execute for, and you can set this on the command line by using the flag `--duration` like this:

```shell
k6 run test.js --duration 30s
```

You can use `s`, `h`, and `m` to define the duration. The following are valid values for this flag, and are all equivalent:
- 1h30m10s
- 5410s
- 90m10s

#### Iterations

You can also set the number of iterations using the command line using the `--iterations` or `-i` flag, like this:

```shell
k6 run test.js --iterations 100
k6 run test.js -i 100
```

In either of the two lines above, k6 will run 100 iterations of the script.


#### Virtual users

You can adjust the number of virtual users on the fly by adding the `-u` or `--vus` flag when running the test:

```shell
k6 run test.js --vus 10 --duration 1m
k6 run test.js -u 10 --iterations 100
```

The two lines above are equivalent, and they both instruct k6 to execute the file `test.js` with 10 virtual users. Each one also sets a test duration and a number of iterations.

```ad-warning
You can only set the number of virtual users if you are _also_ setting the number of iterations, the total test duration, or stages for the test.
```


#### Environment variables

So far, you've learned how to set execution options on the command line, changing test parameters such as virtual users, test duration, the number of iterations, and stages within a test. What if you want to set _other_ variables on the command line?

In that case, you can use environment variables. Environment variables are variables whose values you can set outside of the k6 script.

For example, you could use an environment variable to change the domain used by your test script from the command line. This is useful when you routinely test multiple environments, such as staging and test.

To use an environment variable, define the variable in your script:

```js
import http from 'k6/http';

const hostname = `http://${__ENV.DOMAIN}`;

export default function () {
    let res = http.get(hostname + '/my_messages.php');
}
```

In the script above, `${__ENV.DOMAIN}` is an environment variable, but it is not defined anywhere in the script. Here's how to do define it during runtime:

```shell
k6 run test.js -e DOMAIN=test.k6.io
```

When the test is executed, it will send an HTTP GET request to `http://test.k6.io/my_messages.php`. By using environment variables, you can change the domain that your test targets without changing the script itself.

Despite the name, these variables can hold many types of information, not just information about the environment. Here are some other things you could use an environment variable for:
- think time
- tags you want to affix to requests for this run
- test data file to be used
- pages to exclude or include
- scenario


## Changing settings in k6

In this section, you learned how to use command-line flags and environment variables to change your script executes. You have also previously learned how to set some of these options within the script itself. What happens if there is a conflict between these two ways of changing k6 settings?

k6 always prioritizes settings in this order:
- Command-line flags
- Environment variables
- Exported k6 script options
- Config file
- Defaults

Command-line flags are given the highest priority and always override everything else. Plan your script execution accordingly.

## Test your knowledge

### Question 1

Which of the following commands runs a k6 script?

A: `k6 --vus 1 test.js`

B: `k6 run test.js`

C: `run test.js -vus 1`

### Question 2

Which of the following commands will yield a warning upon execution?

A: `k6 run test.js --duration 10m`

B: `k6 run test.js -i 3`

C: `k6 run test.js --vus 3i`

### Question 3

Which of the following statements about using environment variables is true?

A: The script needs to be updated *and* the environment variable must be set in the command line.

B: Only the script needs to be updated to define the value of the variable.

C: Only the command line flag must be used, and the value will automatically be passed to the script.

### Answers

B, C, A