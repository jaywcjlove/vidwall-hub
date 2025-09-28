<!--idoc:ignore:start-->
> [!TIP]
> Declaration: This project is not an open-source project. The repository serves as the official website, used to collect issues and user demands. This is done to save costs, because without an official website, the application cannot pass the review.
<!--idoc:ignore:end-->

<div align="center">
  <br />
  <br />
  <img src="./assets/logo.png" width="160" height="160">
  <h1>
    Vidwall Hub
  </h1>
  <!--rehype:style=border: 0;-->
  <p>
    <a href="./README.zh.md">简体中文</a> • 
    <a href="https://wangchujiang.com/vidwall/" target="_blank">Vidwall</a> • 
    <a target="_blank" href="https://github.com/jaywcjlove/vidwall-hub/issues/new?template=bug_report.yml">Contact & Support</a> • 
    <a href="./CHANGELOG.md">Changelog</a>
  </p>
  <p>
    <a target="_blank" href="https://github.com/jaywcjlove/vidwall-hub/releases/latest/" title="Vidwall Hub for macOS">
      <img alt="Vidwall Hub AppStore" src="https://jaywcjlove.github.io/sb/download/apple-download.svg" height="51" />
    </a>
  </p>
</div>

![Vidwall Hub](./assets/VidwallHub.gif)

![Vidwall Hub](./assets/vidwall-hub-screenshots-1.png)

**Vidwall Hub** is a tool that allows you to easily import videos (mp4, mov) into the system wallpaper service and use them as lock screen animations in **System Settings**.

When trying to implement both dynamic wallpapers and dynamic lock screens through the [Vidwall](https://github.com/jaywcjlove/vidwall) app, this feature could not be realized due to macOS sandbox restrictions. Therefore, I created a standalone version of the tested code and provide it for free, as a complement to [Vidwall](https://github.com/jaywcjlove/vidwall). Even when running independently and bypassing the sandbox, it still cannot directly set dynamic lock screens because macOS does not provide the related API. Vidwall Hub only imports videos into the system wallpaper service, and users need to complete the final application in the wallpaper options in System Settings.