# mattermost-mattermail

Installs and configures mattermail for the use with a Mattermost instance.

## Requirements

A Debian-based system

## Role Variables

| Name                                 | Required/Default   | Description                                                                                                                                      |
|:-------------------------------------|:-------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------|
| `global_cache_dir`                   | :heavy_check_mark: | Cache directory to download Mattermost files to on the execution machine running this playbook.                                                  |
| `mattermost_mattermail_install_path` | `/opt/mattermail`  | Path where to install the mattermail files to                                                                                                    |
| `mattermost_mattermail_user`         | `www-data`         | User under which mattermail should run. The user has to exist.                                                                                   |
| `mattermost_mattermail_group`        | `www-data`         | Group under which mattermail should run. The group has to exist.                                                                                 |
| `mattermost_mattermail_version`      | `4.0.0-beta.2`     | Version number to download and install. See the [mattermail releases](https://github.com/rodcorsi/mattermail/releases).                          |
| `mattermost_mattermail_config`       | See below          | Object that mostly configures mattermail. For more information see below or the [mattermail repository](https://github.com/rodcorsi/mattermail). |

### `mattermost_mattermail_config`
We use the structure of the config file that is provided by [mattermail repository](https://github.com/rodcorsi/mattermail)
The config object is converted to json, so spelling matters.

| Name        | Required/Default   | Description                            |
|:------------|:-------------------|:---------------------------------------|
| `Directory` | `./data/`          | Location where the cache is stored     |
| `Profiles`  | :heavy_check_mark: | List of objects. For details see below |

#### `Profiles`
| Field             | Required/Default                                                            | Description                                                                                                          |
|-------------------|:---------------------------------------------------------------------------:|----------------------------------------------------------------------------------------------------------------------|
| Name              | :heavy_check_mark:                                                          | Name of profile, used to log                                                                                         |
| Channels          | :heavy_check_mark:                                                          | List of channels where the email will be posted. You can use `#channel` or `@username`                               |
| Email             | :heavy_check_mark:                                                          | Configuration of Email [(details)](https://github.com/stuvusIT/mattermost-mattermail#email)                          |
| Mattermost        | :heavy_check_mark:                                                          | Configuration of Mattermost [(details)](https://github.com/stuvusIT/mattermost-mattermail#mattermost)                |
| MailTemplate      | `:incoming_envelope: _From: **{{.From}}**_\n>_{{.Subject}}_\n\n{{.Message}}` | Template used to format message to post [(details)](https://github.com/stuvusIT/mattermost-mattermail#mailtemplate)  |
| LinesToPreview    | `10`                                                                        | Number of email lines that will be posted                                                                            |
| Attachment        | `true`                                                                      | Inform if attachments will be posted in Mattermost                                                                   |
| Disabled          | `false`                                                                     | Disable this profile                                                                                                 |
| RedirectBySubject | `true`                                                                      | Inform if redirect email by subject [(details)](https://github.com/stuvusIT/mattermost-mattermail#redirectbysubject) |
| Filter            | ` `                                                                         | Filter used to redirect email [(details)](https://github.com/stuvusIT/mattermost-mattermail#filter)                  |

#### Email

Email configuration, used to access IMAP server

| Field             | Required/Default   | Description                                                        |
|-------------------|:------------------:|--------------------------------------------------------------------|
| ImapServer        | :heavy_check_mark: | Address of imap server with port number ex: _imap.example.com:143_ |
| Username          | :heavy_check_mark: | Email address or username used authenticate on email server        |
| Password          | :heavy_check_mark: | Password used authenticate on email server                         |
| StartTLS          | `false`            | Enable StartTLS connection if server supports                      |
| TLSAcceptAllCerts | `false`            | Accept insecure certificates with TLS connection                   |

#### Mattermost

Mattermost configuration

| Field    | Required/Default   | Description                                                                                                                |
|----------|:------------------:|----------------------------------------------------------------------------------------------------------------------------|
| Server   | :heavy_check_mark: | Address of Mattermost server. Please inform protocol and port if its necessary ex: _<https://mattermost.example.com:8065>_ |
| Team     | :heavy_check_mark: | Team name                                                                                                                  |
| User     | :heavy_check_mark: | User used to authenticate on Mattermost server                                                                             |
| Password | :heavy_check_mark: | Password used to authenticate on Mattermost server                                                                         |
| UseAPIv3 | `true`             | Set to use Mattermost Api V3                                                                                               |

#### MailTemplate

This configuration formats email message using markdown to post on Mattermost.
The default configuration is `:incoming_envelope: _From: **{{.From}}**_\n>_{{.Subject}}_\n\n{{.Message}}`, in this example when Mattermail receives a message from `john@example.com`, with subject `Hello world` and message body `Hi I'm John`. This email will be formated to:

:incoming_envelope: _From: **john@example.com**_
>_Hello world_

Hi I'm John

#### RedirectBySubject

If the option `RedirectBySubject` is `true` the Mattermail will try to redirect an email and post it using the subject, ex:

| Subject                     | Destination                         |
|-----------------------------|-------------------------------------|
| [#orders] blah              | channel `orders`                    |
| [#orders #info] blah        | channel `orders` and `info`         |
| Fwd [#orders] [#info] blah  | channel `orders` and `info`         |
| [1234#orders] foo           | channel `orders`                    |
| [@john] blah                | user `john`                         |
| [@john #orders] blah        | user `john` and channel `orders`    |

#### Filter

This option is used to redirect email following the rules.

```yml
Filter:
  - Subject: Feature
    Channel: #feature
  - From: test@gmail.com
    Subject: To Me
    Channel: @test2
  - From: @companyb.com
    Channel: #companyb
```

## Example Playbook

```yml
global_cache_dir: "{{ lookup('env', 'HOME') }}/.cache/stuvus"
mattermost_mattermail_config:
  Directory: "./data/"
  Profiles:
    - Name: Orders
      Channels: 
        - orders
      Email:
        ImapServer: imap.example.com:143
        Username: orders@example.com
        Password: password
      Mattermost:
        Server: https://example.mattermost.com
        Team: team1
        User: mattermail@example.com
        Password: password
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).


## Author Information

- [Fritz Otlinghaus (Scriptkiddi)](https://github.com/scriptkiddi) _fritz.otlinghaus@stuvus.uni-stuttgart.de_
