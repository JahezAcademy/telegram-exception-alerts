![](https://telegram.org/img/t_logo.svg?1)
# Telegram Exception Alerts
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![PyPI](https://img.shields.io/pypi/v/telegram-exception-alerts)](https://pypi.org/project/telegram-exception-alerts/)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/telegram-exception-alerts)
![PyPI - Wheel](https://img.shields.io/pypi/wheel/telegram-exception-alerts)
![PyPI - Downloads](https://img.shields.io/pypi/dw/telegram-exception-alerts)

**THIS LIBRARY HAS BEEN EXTENSIVELY TESTED AND IS CONSIDERED VERY STABLE: IT WORKS FINE AND HAS NO EXTERNAL DEPENDENCIES. SO NO FUTURE UPDATES ARE EXPECTED UNLESS I HAVE SOME VERY BRIGHT IDEA ABOUT IT.**

A very lightweight library for sending exception details to Telegram using a decorator. It uses no external dependencies.

## Installation

```bash
pip install telegram-exception-alerts
```
or
```bash
poetry add telegram-exception-alerts
```

## Usage

After you initialize the alerter instance you can attach the decorator to any function. If it
raises an exception information will be send to the chat specified in `chat_id` (don't forget
that if you want to send notification to a channel you need to prepend that `chat_id` with `-100`).

### Normal initialization

```python
from telegram_exception_alerts import Alerter

tg_alert = Alerter(bot_token='YOUR_BOT_TOKEN', chat_id='YOUR_CHAT_ID')

@tg_alert
def some_func_that_can_raise_an_exception():
    raise RuntimeError('this is an exception')
```

### Initialization from environment (recommended)

You can also initialize the alerter from environment variables. **This is the recommended way**
because it will make sure you're not committing sensitive information to the repo.

* `ALERT_BOT_TOKEN` - your bot token
* `ALERT_CHAT_ID` - your chat id to receive notifications

```python
from telegram_exception_alerts import Alerter

tg_alert = Alerter.from_environment()

@tg_alert
def some_func_that_can_raise_an_exception():
    raise RuntimeError('this is an exception')
```

Here's what a telegram message from an example above looks like:

<img src="./message_example.png" width="400">


### Blacklists and Whitelists

You can use the `blacklist` and `whitelist` parameters of the `Alerter` class to control which errors should be sent to your Telegram channel and which errors should be excluded.


#### Blacklist

The `blacklist` parameter allows you to specify which errors should be excluded from the alerts. Any error type present in the `blacklist` will trigger a message to be sent to the Telegram channel.

##### Example
```python
from telegram_exception_alerts import Alerter

tg_alert = Alerter(bot_token='YOUR_BOT_TOKEN', chat_id='YOUR_CHAT_ID', blacklist=["ValueError"])

@tg_alert
def raise_runtime_error():
    raise RuntimeError('This is a RuntimeError')

```

In the above example, the `RuntimeError` will not be included in the `blacklist`, so a message will not be sent to the Telegram channel when this exception occurs.


```python
from telegram_exception_alerts import Alerter

tg_alert = Alerter(bot_token='YOUR_BOT_TOKEN', chat_id='YOUR_CHAT_ID', blacklist=["ValueError"])

@tg_alert
def raise_value_error():
    raise ValueError('This is a ValueError')

```

In the above example, the ValueError is included in the blacklist, so a message will be sent to the Telegram channel when this exception occurs.


#### Whitelist

The `whitelist` parameter allows you to specify which errors **should not** be included in the alerts. Only the error types present in the whitelist will not trigger a message to be sent to the Telegram channel.


```python
from telegram_exception_alerts import Alerter

tg_alert = Alerter(bot_token='YOUR_BOT_TOKEN', chat_id='YOUR_CHAT_ID', whitelist=["RuntimeError"])

@tg_alert
def raise_runtime_error():
    raise RuntimeError('This is a RuntimeError')

```

In the above example, the `RuntimeError` is included in the `whitelist`, so a message will not be sent to the Telegram channel for this exception. Other exceptions will trigger a message to be sent.


## Sending messages
You can also use the `Alerter` as a simple way to send messages to Telegram:

```python
from telegram_exception_alerts import Alerter

tg_alert = Alerter.from_environment()

tg_alert.send_message(chat_id=111222333, text='Message text')
```

For real bot programming I highly recommend the excellent [python-telegram-bot](https://python-telegram-bot.org/) library.
