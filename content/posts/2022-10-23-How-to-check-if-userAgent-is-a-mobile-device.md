---
title: "How to Check if UserAgent Is a Mobile Device"
date: 2022-10-23T20:37:53+02:00
tags:
    - react
---


`userAgent` is a string value which will be set to any one of the following device names if they are using a mobile device:

Android, BlackBerry, iPhone, iPad, iPod, Opera Mini, IEMobile, or WPDesktop.

```js
export default function useDeviceDetect() {
  const [isMobile, setMobile] = React.useState(false);

  React.useEffect(() => {
    const userAgent =
      typeof window.navigator === "undefined" ? "" : navigator.userAgent;
    const mobile = Boolean(
      userAgent.match(
        /Android|BlackBerry|iPhone|iPad|iPod|Opera Mini|IEMobile|WPDesktop/i
      )
    );
    setMobile(mobile);
  }, []);
}

```