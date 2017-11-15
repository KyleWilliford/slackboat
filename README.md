# Slackboat chat bot server

## Wait, what is this?

This is the server for the Slackboat Slack app. What's that? Never heard of it? Makes sense. Slackboat is a Slack chat bot that I created and it is not publicly distributed.

The Slackboat _bot_ adds some slash commands to your workspace. Read the next few sections for more detail on what is added and how to use these commands.

The Slackboat _server_ processes commands from users who have installed the _bot_ into their workspace. The _server_ also implements a method for authorizing installation of the app into your workspace.

This server's api paths and method types are listed near the end of this readme.

### Why did you name this "slackboat"

'Cause it is sort of similar to slackbot, I guess? Also, ~phishing~ fishing is sometimes done ~by bots~ on boats. That is sort of related, right?

## Install _Slackboat App_ to Your Workspace (live version)

Click the following button to sail the Slackboat into your workspace.

<a href="https://slack.com/oauth/authorize?scope=commands&client_id=261022332754.262110268791"><img alt="Add to Slack" height="40" width="139" src="https://platform.slack-edge.com/img/add_to_slack.png" srcset="https://platform.slack-edge.com/img/add_to_slack.png 1x, https://platform.slack-edge.com/img/add_to_slack@2x.png 2x" /></a>

Note: this request communicates with a live version of this Slackboat _server_, deployed on an AWS EC2 instance.

## Now what?

Open up your slack client and try out the new commands!

These are the currently available commands:
`/is_spam_domain [email, or domain]` Checks a given email/domain string against a list of known disposable spam domains, maintained here: https://github.com/martenson/disposable-email-domains. The domain is the only relevant piece - anything before the `@` is stripped out.

`/is_in_phishtank [url]` Checks a given url against a list of known phishy urls, maintained here: https://www.phishtank.com/

`/is_in_openphish [url]` Checks a given url against a list of known phishy urls, maintained here: https://openphish.com/phishing_feeds.html

`is_zeus_domain [domain]` Checks a given domain against a list of known ZeuS trojan domains, maintained here: 
https://zeustracker.abuse.ch/blocklist.php?download=baddomains

`/is_zeus_ipv4 [ipv4]` Checks a given ipv4 address against a list of known ZeuS trojan ip addresses, maintained here:
https://zeustracker.abuse.ch/blocklist.php?download=badips

`/find_any_match [text]` Checks a given text input against all of the above databases.

No natural language processing is applied to these terms. An exact match (case insensitive) is all that is checked.

![Alt text](/images/slash_commands_1.png?raw=true "slash command set 1")
![Alt text](/images/slash_commands_2.png?raw=true "slash command set 2")

![Alt text](/images/find_any_result.png?raw=true "find any result")


# Development, running this server locally, tunneling, and deployment

For ease of development locally, use ngrok: https://ngrok.com/
---
This will make it possible for your Slack client to send requests from users (i.e. you or whoever has install the app) to your local server over the internet. The point of this is that you will not have to deploy this server to a remote environment (an AWS EC2 node, for example) every time you want to test a change during development.

## Requirements

A Not Terrible Internet Connection (duh)

Java JDK 8 (compile) / JRE 8 (runtime)

Maven 3.x.x

ngrok 2.8.x (development, localhost)

Up-to-date version of a Slack client

## Install ngrok:

Go here https://ngrok.com/ and download the package for your operating system.

unzip the archive (command instructions are on the same page as the downloads)

For this project, you will expose localhost to the internet on port 8080 with a randomized domain name.

run  `$ ./ngrok http 8080`

You should see something similar to this:

![Alt text](/images/ngrok-http-8080.png?raw=true "ngrok http 8080")

More documentation on ngrok: https://ngrok.com/docs/2

You will need to configure the slack app to send requests to the tunneled domain. Look at the ngrok output to get the public domain.

## How do I start the server?

1. Install dependencies (listed above)
1. Clone the repo
1. Change directory to repo `cd <path to repo>`
1. Run `mvn clean install` to build the application
1. Start the application with `java -jar target/slackboat-<version>.jar server config.yml`
1. A helpful Linux/Mac alias to do this: `alias slackboat='cd <path to repo>; mvn clean install; java -jar target/slackboat-<version>.jar server config.yml'` (check the current version in the pom.xml file)

### URLs

Client OAuth Redirect URL:

`<server url>/auth`

Slash Command URLs:

`<server url>/api/<command path>`

## Server REST API

`POST /api/is_blacklisted` Checks a given email against a list of known bad boy disposable spam domains, maintained here: https://github.com/martenson/disposable-email-domains

`POST /api/is_phishy` Checks a given url against a list of known phishy urls, maintained here: https://www.phishtank.com/ and here https://openphish.com/phishing_feeds.html

`POST /api/is_zeus_domain` Checks a given domain against a list of known ZeuS trojan domains, maintained here: 
https://zeustracker.abuse.ch/blocklist.php?download=baddomains

`POST /api/is_zeus_ipv4` Checks a given ipv4 address against a list of known ZeuS trojan ip addresses, maintained here:
https://zeustracker.abuse.ch/blocklist.php?download=badips

`POST /api/find_any_match` Checks a given text input against all of the above databases.

# Future Improvements and TODOs

- The relevant databases should be retrieved at server start, and periodically after that. This server currently loads files that were retrieved between October 24th 2017 and November 2nd 2017, or thereabouts, so the data is not current.
- Manage public app distribution
- Add slack permission scope (required for public distribution)
- Enable HTTPS (required for public distribution)
- Encrypt client secret (required for public distribution)
- Review Slack app distrubution terms of service/developer guides (required for public distribution)
- More content features and stuff? Interactivity? More slash commands that do different things?

# Travis CI

https://travis-ci.org/KyleWilliford/slackboat

