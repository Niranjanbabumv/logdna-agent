# logdna-agent

[![Build Status](https://travis-ci.org/logdna/logdna-agent.svg?branch=master)](https://travis-ci.org/logdna/logdna-agent)
[![Build status](https://ci.appveyor.com/api/projects/status/mk5rb0uk6xkjxhk2/branch/master?svg=true)](https://ci.appveyor.com/project/mikehu/logdna-agent/branch/master)

LogDNA's collector agent which streams log files to your LogDNA account.

## Getting Started

### From an Official Release

Check out the offical [LogDNA site](https://logdna.com/) on how to get started from a released version of LogDNA CLI and agent.

### From Source

Follow these quick instructions to run the LogDNA agent from source

```bash
git clone https://github.com/logdna/logdna-agent.git
cd logdna-agent
npm install

# help
sudo node index.js --help

# configure
sudo node index.js -k <YOUR LOGDNA AGENT KEY>
# On Linux, this will generate a config file: /etc/logdna.conf.
# On Windows, this will generate a config file: C:\ProgramData\logdna\logdna.conf.
# /var/log is monitored/added by default (recursively), optionally specify more folders here
sudo node index.js -d /path/to/log/folders

# start the agent
sudo node index.js
```

### Configuration File

Instead of generating a config file, you can create your own config file: `logdna.conf`:

```conf
logdir = /var/log/myapp
key = <YOUR LOGDNA KEY>
autoupdate = 1
```
On Windows, you can use Windows paths, just make sure to use `\\` as a separator:

```conf
logdir = C:\\Users\\username\\AppData\\logdna
key = <YOUR LOGDNA KEY>
autoupdate = 1
```

## Windows Event Log

The LogDNA agent supports reading from the Application log on Windows. To stream these events, you'll need to add an additional property to your configuration file:

```
windowseventlogprovider = yourprovidername
```

Then in a C# application you can do:

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Diagnostics;
namespace LogDNATest
{
    class Program
    {
        static void Main(string[] args)
        {
            string source = "yourprovidername";
            string log = "application";
            string eventMessage = "someevent";

            if (!EventLog.SourceExists(source))
            {
                EventLog.CreateEventSource(source, log);
            }

            while(true)
            {
                EventLog.WriteEntry(source, eventMessage);
                Console.WriteLine("logged event message");
                System.Threading.Thread.Sleep(1000);
            }
                
        }
    }
}
```

And you'll see your event log messages appear on the LogDNA dashboard.

## How it Works

The LogDNA agent authenticates using your LogDNA agent key and opens a secure web socket to LogDNA's ingestion servers. It then 'tails' for new log files added to your specific logging directories, watching for file changes. Those changes are sent to to LogDNA via the secure web socket.

## Contributing

Contributions are always welcome. See the [contributing guide](/CONTRIBUTING.md) to learn how you can help.
