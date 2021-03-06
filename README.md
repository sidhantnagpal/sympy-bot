# SymPy Bot

This is a GitHub bot for SymPy. It runs on Heroku and uses the
[@sympy-bot](https://github.com/sympy-bot) GitHub user.

The bot makes sure that every pull request to SymPy has a release notes entry
in the pull request description. It then automatically adds these notes to the
[release notes](https://github.com/sympy/sympy/wiki/Release-Notes) on the wiki
when the pull request is merged.

See [the guide on the SymPy
wiki](https://github.com/sympy/sympy/wiki/Writing-Release-Notes) on how to
write release notes.

The bot may also do other things in the future. If you have any suggestions or
have found any bugs, please open an
[issue](https://github.com/sympy/sympy-bot/issues). Pull requests are welcome
too.

## Setting up the bot

[This tutorial](https://github-bot-tutorial.readthedocs.io/en/latest) is very
good on how to set up Heroku and write GitHub bots. This bot is based on it.

The bot is tied to Heroku, so any push to this repo is automatically deployed
there (it is currently configured to not deploy until the
[Travis](https://travis-ci.org/sympy/sympy-bot) tests pass). The Heroku
dashboard is at https://dashboard.heroku.com/apps/sympy-bot

If the bot stops working, look at the logs with

    heroku logs -a sympy-bot

(you need to install the `heroku` command line tools and have access to the
sympy-bot Heroku app). You can also see the logs on
[Heroku](https://dashboard.heroku.com/apps/sympy-bot/logs).

Next you need to set up the bot on GitHub. To do so, follow these steps:

1. Go to the webhooks settings (for instance, at
   https://github.com/sympy/sympy/settings/hooks), and create a new webhook.

   - Set the payload URL to the Heroku app url (for instance,
     https://sympy-bot.herokuapp.com/)
   - Set the content type to `application/json`
   - Generate a random password for the secret. I used the keychain app on my
     Mac to generate a 20 character password with random characters. Save this
     secret, as you will need to enter it in Heroku as well.
   - Under "Which events would you like to trigger this webhook?" select "Let
     me select individual events.". Then make sure only **Pull requests** is
     checked.
   - Make sure **Active** is checked

2. Go to Heroku and under the settings UI (e.g.,
   https://dashboard.heroku.com/apps/sympy-bot/settings), create two config
   variables:

   - `GH_SECRET`: set this to the secret you created in step 1 above
   - `GH_AUTH`: set this to the personal access token for the `sympy-bot`
     user. If you don't have this or need to regenerate it, login as the bot
     user and go to the personal access token settings (at
     https://github.com/settings/tokens), and create a new token. **VERY
     IMPORTANT:** Give the token `public_repo` access only.

3. Give the `sympy-bot` user push access to the repo. This is required for the
   bot to set commit statuses and to push to the wiki. If you know how to
   allow it to do this without giving it as much access, please let me know. I
   have tried playing with using reviews instead of statuses, but I couldn't
   get it to work.

Run

    heroku labs:enable runtime-dyno-metadata -a sympy-bot

to enable the bot version environment variable (run this if the version is
"version not found!").

## Testing

There is a testing deploy set up at
https://dashboard.heroku.com/apps/sympy-bot-testing. Currently it is
configured to run on https://github.com/asmeurer/GitHub-Issues-Test.

To test, push to a separate branch (`master` has branch protection) on this
repo (you can also set up a separate testing deploy for your fork if you
want). Then go to
https://dashboard.heroku.com/apps/sympy-bot-testing/deploy/github and manually
deploy the branch.
