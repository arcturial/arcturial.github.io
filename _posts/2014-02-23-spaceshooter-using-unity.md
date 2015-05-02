---
layout: post
title: Callback Support For Clickatell PHP Library
fullview: true
---

Wanting to create a game for a while now, I never knew where to start. Finally I decided to just take some tutorials and work through them.

I followed a tutorial for creating a [Space Shooter with Unity 3D](http://unity3d.com/learn/tutorials/projects/space-shooter) and this is what I came up with. Nothing unique or exciting, just getting to know the application and a little more about game programming/design. All assets used in this game was provided by [Unity 3D](http://unity3d.com/).

Hopefully this isn’t the last game that will be posted. Enjoy...

<script type="text/javascript">
<!--
var unityObjectUrl = "http://webplayer.unity3d.com/download_webplayer-3.x/3.0/uo/UnityObject2.js";
if (document.location.protocol == 'https:')
    unityObjectUrl = unityObjectUrl.replace("http://", "https://ssl-");
document.write('<script type="text/javascript" src="' + unityObjectUrl + '"></script>');
-->
</script>
<script type="text/javascript" src="http://webplayer.unity3d.com/download_webplayer-3.x/3.0/uo/UnityObject2.js"></script>
<div class="videowrapper">
<div id="unityPlayer" style="position: absolute; height: 100%; width: 100%; top: 0px; left: 0px;"><embed style="display: block; width: 100%; height: 100%;" firstframecallback="UnityObject2.instances[0].firstFrameCallback();" type="application/vnd.unity" src="http://www.cainsvault.com/wordpress/wp-content/uploads/other/Space_Shooter/Space_Shooter.unity3d" height="100%" width="100%"></div>
</div>
<script type="text/javascript">
$(document).ready(function() {
    var u = new UnityObject2();
    u.initPlugin(jQuery("#unityPlayer")[0], "http://arcturial.github.io/downloads/Space_Shooter.unity3d");
});
</script>