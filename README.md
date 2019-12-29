# ESP ADF component override test

These are two applications taken from the getting started guides for ESP ADF and IDF which were extended in order to test component overriding.

The two applications are:
* $ADF_PATH/examples/getting_started/play_mp3
* $ADF_PATH/esp-idf/examples/getting_started/hello-world

Dummy components with the names same as two components in ADF and IDF were added to the apps.

I'm working on an ADF application for a while and now I wanted to update my ADF copy to the latest master (commit `fae539c3035b2c041f49c5b01cdc4c99038595b0`) which includes ESP-IDF v3.3. After updating my ADF copy I'm experiencing a problem when overriding ADF components. With the latest master I'm not able to build my app. In the output from `idf.py build` in the `Component paths:` section I see that the components are taken from the `$ADF_PATH/components` and not from `<PROJECT_PATH>/components`.

## Steps to reproduce the problem:

1. `git clone --recursive https://github.com/espressif/esp-adf.git`
2. setting the envvars:
```
export ADF_PATH=/<path-to-esp-adf>
export IDF_PATH=$ADF_PATH/esp-idf
export PATH=$IDF_PATH/tools:$PATH
export PATH=/<path-to-xtensa-esp32-elf-5.2.0>/bin:$PATH
```
3. Prepare a demo project: copied IDF hello-world and ADF play_mp3 examples to a new folder and initialized it as a git repo.
```
$IDF_PATH/examples/getting-started/hello-world
$ADF_PATH/examples/getting-started/play_mp3
```

4. build the apps with `idf.py build` to check if everything works

5. added dummy components which override the standard components: `wifi_service` for ADF and `wifi_provisioning` for IDF and I've tried to rebuild the projects:

The **expected result** was that both the projects build successfully and the ADF/IDF components are overridden by the components in the project.

The **actual result** is: 
* the IDF `hello_world` project was build with the IDF `wifi_provisioning` component overridden with the one from the project;
* for the ADF `play_mp3` project, the `wifi_service` component was **not overriden** with the one from the project. I also checked `Component paths:` list from the `idf.py build` output and I saw that the path was `$ADF_PATH/components/wifi_service`.

6. I continued testing with older commits to find which commit breaks the component overrides and I found out that problem starts with the commit with hash `e9c4faff248cd300207ad938a93d67d880484986`.

```
commit e9c4faff248cd300207ad938a93d67d880484986
Author: maojianxin <maojianxin@espressif.com>
Date:   Fri Dec 13 13:52:43 2019 +0800

    Change COMPONENT_NAME to COMPONENT_TARGET for support IDF v3.3.1
```
