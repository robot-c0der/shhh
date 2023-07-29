<p align="center">
  <img width="100px" src="https://github.com/smallwat3r/shhh/blob/master/shhh/static/img/logo.png" />
</p>
<p align="center">Keep secrets out of emails and chat logs.</p>

<p align="center">
  <a href="https://travis-ci.com/smallwat3r/shhh" rel="nofollow"><img src="https://travis-ci.com/smallwat3r/shhh.svg?branch=master" style="max-width:100%;"></a>
  <a href="https://codecov.io/gh/smallwat3r/shhh" rel="nofollow"><img src="https://codecov.io/gh/smallwat3r/shhh/branch/master/graph/badge.svg" style="max-width:100%;"></a>
  <a href="https://codeclimate.com/github/smallwat3r/shhh/maintainability" rel="nofollow"><img src="https://api.codeclimate.com/v1/badges/f7c33b1403dd719407c8/maintainability" style="max-width:100%;"></a>
  <a href="https://github.com/smallwat3r/shhh/blob/master/LICENSE" rel="nofollow"><img src="https://img.shields.io/badge/License-MIT-green.svg" style="max-width:100%;"></a>
</p>

## What is it?

**Shhh** is a tiny Flask app to create encrypted secrets and share 
them securely with people. The goal of this application is to get rid
of plain text sensitive information into emails or chat logs.  

Shhh is deployed [here](https://www.shhh-encrypt.com) (_temporary unavailable
until new deployment solution_), but **it's better for organisations and people 
to deploy it on their own personal / private server** for even better security. 
You can find in this repo everything you need to host the app yourself.  

Or you can **one-click deploy to Heroku** using the below button.
It will generate a fully configured private instance of Shhh 
immediately (using your own server running Flask behind Gunicorn and Nginx, 
and your own Postgres database, **for free**).  

[![Deploy][heroku-shield]][heroku]  

Also, checkout [shhh-cli](https://github.com/smallwat3r/shhh-cli), 
a Go client to interact with the Shhh API from the command line.  

## How does it work?

The sender has to set an expiration date along with a passphrase to
protect the information they want to share.  

A unique link is generated by Shhh that the sender can share with the
receiver in an email, alongside the temporary passphrase they created
in order to reveal the secret.  

The secret will be **permanently removed** from the database as soon 
as one of these events happens:  

* the expiration date has passed. 
* the receiver has decrypted the message. 
* the amount of tries to open the secret has exceeded. 

The secrets are encrypted in order to make the data anonymous, 
especially in the database, and the passphrases are not stored 
anywhere.  

_Encryption method used: Fernet with password, random salt value and
strong iteration count (100 000)._  

_Tip: for better security, avoid writing any info on how/where to use the secret you're sharing (like urls, websites or emails). Instead, explain this in your email or chat, with the link and passphrase generated from Shhh. So even if someone got access to your secret, there is no way for the attacker to know how and where to use it._

## Is there an API?

Yes, you can find some docs [here](https://app.swaggerhub.com/apis-docs/smallwat3r/shhh-api/1.0.0).  

## How to launch Shhh locally?

These instructions are for development purpose only. For production 
use you might want to use a more secure configuration.

<details>
<summary><b>Launch it natively</b></summary>

#### Deps  

Make sure you have `make`, `yarn`, and obviously `python@3.8` 
installed on your machine.  

#### Postgres  

You will need a Postgres server running locally in the background. 
Create a database named `shhh`.  

```sql
CREATE DATABASE shhh;
```

#### Flask  

You will need to set up a few environment variables. We use them to 
configure Flask, as well as the application connection to the 
database.  

Rename the file `/environments/local.dev.template` to
`/environments/local.dev` and fill in the missing variables 
(these are the variables needed to connect to your local Postgres database).  

Once done, from the root of the repository, run:  

```
make local
```

This command will make sure a virtual environment is created and that
all the needed dependencies are installed, and finally launch a flask
local server.  

You can now access the app at http://localhost:5000  

</details>

<details>
<summary><b>Launch it with docker-compose</b></summary>

#### Deps

Make sure you have `make`, `docker` and `docker-compose` installed on
your machine.  

The application will use the development env variables from [/environments/docker.dev](https://github.com/smallwat3r/shhh/blob/master/environments/docker.dev).  

#### Docker

From the root of the repository, run

```sh
make dc-start  # to start the app (or dc-start-adminer to use adminer)
make dc-stop   # to stop the app
```

Once the container image has finished building and has started, you 
can access:  

* Shhh at http://localhost:5000
* (and access the database records using Adminer on port `8080` if you launched Shhh with adminer)

Note: When started with the docker-compose set-up, the application is running with Gunicorn.

</details>

<details>
<summary><b>Run development checks</b></summary>
<br>

You can run tests and linting / security reports using the Makefile:  

```sh
make checks  # run all checks

make tests   # run tests
make pylint  # run Pylint report
make bandit  # run Bandit report
make mypy    # run Mypy report
```

</details>

## Environment variables

Bellow is the list of environment variables used by Shhh.  

<details>
<summary><b>Mandatory</b></summary>

* `FLASK_ENV`: the environment config to load (`testing`, `dev-local`, `dev-docker`, `heroku`, `production`).
* `POSTGRES_HOST`: Postgresql hostname
* `POSTGRES_USER`: Postgresql username
* `POSTGRES_PASSWORD`: Postgresql password
* `POSTGRES_DB`: Database name

</details>

<details>
<summary><b>Optional</b></summary>

* `SHHH_HOST`: This variable can be used to specify a custom hostname to use as the
domain URL when Shhh creates a secret (ex: `https://<domain-name.com>`). If not set, the hostname 
defaults to request.url_root, which should be fine in most cases.
* `SHHH_SECRET_MAX_LENGTH`: This variable manages how long the secrets your share with Shhh can 
be. It defaults to 250 characters.
* `SHHH_DB_LIVENESS_RETRY_COUNT`: This variable manages the number of tries to reach the database 
before performing a read or write operation. It could happens that the database is not reachable or is 
asleep (for instance this happens often on Heroku free plans). The default retry number is 5.
* `SHHH_DB_LIVENESS_SLEEP_INTERVAL`: This variable manages the interval in seconds between the database
liveness retries. The default value is 1 second.

</details>

## Acknowledgements

Special thanks: [@AustinTSchaffer](https://github.com/AustinTSchaffer), [@kleinfelter](https://github.com/kleinfelter)  

## License

See [LICENSE](https://github.com/smallwat3r/shhh/blob/master/LICENSE) file.  

## Contact

Please report issues or questions 
[here](https://github.com/smallwat3r/shhh/issues).  


[![Buy me a coffee][buymeacoffee-shield]][buymeacoffee]


[buymeacoffee-shield]: https://www.buymeacoffee.com/assets/img/guidelines/download-assets-sm-2.svg
[buymeacoffee]: https://www.buymeacoffee.com/smallwat3r

[heroku-shield]: https://www.herokucdn.com/deploy/button.svg
[heroku]: https://heroku.com/deploy?template=https://github.com/smallwat3r/shhh
