# установка среды для deepcase-app
#### УСТАНОВКА DOCKER


---
## Install docker

Open [https://docs.docker.com/desktop/mac/install/](https://docs.docker.com/desktop/mac/install/), then choose your chip type for .dmg downloading:

![Screenshot 2022-03-19 at 15.35.59.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2b0299b3-2c1e-4f61-8edf-576b818221bd/Screenshot_2022-03-19_at_15.35.59.png)

Simple step to install:

![Screenshot 2022-03-19 at 15.29.49.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dcf98ba4-5f17-434b-a42d-ea8a78587473/Screenshot_2022-03-19_at_15.29.49.png)

And go on, to open docker.

![Screenshot 2022-03-19 at 15.31.52.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9da5c493-161d-4086-ab34-5980beef6d1a/Screenshot_2022-03-19_at_15.31.52.png)

So, now you can see docker icon in your top bar, congratulations!

![Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b6d15120-4680-4a5f-8458-1bccdc43fe8d/Untitled.png)

## Install node

We trust nvm to manage node versions, just exec

```bash
curl -o- <https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh> | bash
```

or

```bash
wget -qO- <https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh> | bash
```

and next

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh" # This loads nvm
```

Now, when nvm is installed, install node ver for deep:

```bash
nvm install 14.15.0
```

## Download app

[](https://github.com/deep-foundation/deepcase/suites/6122480781/artifacts/213071506)

![Screenshot 2022-04-14 at 19.23.28.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/588008ff-26e5-4015-92c9-204215f0a479/Screenshot_2022-04-14_at_19.23.28.png)

After run you can see.

![Screenshot 2022-04-14 at 19.23.49.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/23ea3d1e-135c-473f-81bd-62bc687d2731/Screenshot_2022-04-14_at_19.23.49.png)

Click `run engine` to initialize docker containers with `PostgreSQL`, `Hasura` and `Deep.Links` and wait untill progress bar indicates loading.

![Screen Recording 2022-03-18 at 23.49.16.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/257c7e1c-ac15-422e-80c1-2943e969f7c8/Screen_Recording_2022-03-18_at_23.49.16.gif)

<aside> ❕ Bug with disabled run engine. We already known. Cut and put again path into input.

</aside>

After running, you can see this:

![Screenshot 2022-02-23 at 09.42.58.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf7b3647-43ef-416a-abd3-2f87d0d51a46/Screenshot_2022-02-23_at_09.42.58.png)

<aside> ❕ If you see other anomaly result, please write comment here in notion, or create issue here.

</aside>