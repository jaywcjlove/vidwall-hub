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

**Vidwall Hub** is a tool that makes it easy to import videos (mp4, mov) into the system wallpaper service and use them as lock screen animations through the system settings. Due to sandbox restrictions, [Vidwall](https://github.com/jaywcjlove/vidwall) cannot implement this functionality directly within the app. As a complement to [Vidwall](https://github.com/jaywcjlove/vidwall), Vidwall Hub also cannot set lock screen animations directly because macOS does not provide the necessary API. It only imports the videos into the system wallpaper service, and users need to apply them through the wallpaper options in the system settings.