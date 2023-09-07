# установка среды для deepcase-app
#### УСТАНОВКА DOCKER


---
# Install Windows [Deep.Case](http://Deep.Case) application

## Install docker

download and install as is [https://docs.docker.com/desktop/windows/install/](https://docs.docker.com/desktop/windows/install/)
![](__content__/install-windows-1.png)

Reboot after install

![](__content__/install-windows-2.png)

Make your choice

![](__content__/install-windows-3.png)

You may see “WSL2 Installation is incomplete”. Before clicking restart in window below - download and install kernel update [https://aka.ms/wsl2kernel](https://aka.ms/wsl2kernel)

![](__content__/install-windows-4.png)

![](__content__/install-windows-5.png)

Click restart, and if all is ok - you will see:

![](__content__/install-windows-6.png)

## Install node

We trust nvm to manage node versions just download installer
[github](https://github.com/coreybutler/nvm-windows/releases/download/1.1.9/nvm-setup.zip)

After install you need to configure nvm

!! use cmd or PowerShell with administrative rights !!

```powershell
nvm install 14.15.0

nvm install 14.15.0
```


## Download app

[github](https://github.com/deep-foundation/deepcase/suites/6122480787/artifacts/213058939)


![](__content__/install-windows-7.png)

Unzip installer

![](__content__/install-windows-8.png)
After install open and you will see


![](__content__/install-windows-9.png)

Click `run engine` to initialize docker containers with `PostgreSQL`, `Hasura` and `Deep.Links` and wait untill progress bar indicates loading.


![](__content__/install-windows-10.gif)

<aside> ❕ Bug with disabled run engine. We already known. Cut and put again path into input.

</aside>

After running, you can see this:


![](__content__/install-windows-11.png)

<aside> 💡 If you see other anomaly result, please write comment here in notion, or create issue here.

</aside>


---