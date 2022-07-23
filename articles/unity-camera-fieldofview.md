---
title: "Unityのカメラ画角は垂直画角"
emoji: "🕌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [unity]
published: true
---

## はじめに

Unityが提供するカメラコンポーネントは、画角（Field Of View）を垂直画角として計算します。そのため、アスペクト比を変えると、垂直方向の視界はそのままで、水平方向の視界だけが変化します。

本記事ではエディタ上でアスペクト比を変えた時に、カメラが映す画面がどう変化するかの実例をまとめました。
加えて、Unityのカメラ画角を水平画角に変えるスクリプトも紹介します。

## Unityのカメラ画角

Unity標準のカメラの画角は、垂直画角として計算されます。

垂直画角は図示すると以下の画像の通りで、レンズから見える範囲の上下の両端を結んで出来る角度のことです。
![](/images/2022/07/16/08.png)

インスペクタ上では、カメラの画角を「Field Of View」で操作できます。
![](/images/2022/07/16/06.png)

一見「Field Of View Axis」をHorizontalに変更すれば、水平画角に変更できそうです。しかし、「Field Of View Axis」をHorizontalに指定した時の「Field Of View」の値は、「現在の垂直画角」と「現在のアスペクト比」から算出される水平画角であり、内部で持っている値は垂直画角のままです。

![](/images/2022/07/16/07.png)

事実 `UnityEngine.Camera.fieldOfView` の値は、常に垂直画角が入っています。

実際に画角と画面のアスペクト比をいじってみます。

検証のために、簡単なグリッドシェーダを用意しました。このグリッドシェーダを巨大なQuadに適用して、カメラに映しています。

![](/images/2022/07/16/09.png)

垂直画角を60度に固定したままアスペクト比を変えるとどうなるかみてみます。

アスペクト比 16:9
![](/images/2022/07/16/01.png)

アスペクト比 4:3
![](/images/2022/07/16/02.png)

見比べると、縦の視界は変化せず、横の視界だけが変化しています。

このことから、垂直画角を基準にして、画面のアスペクト比から水平方向の視界を決定していることがわかりました。

（Free Aspectモードでアスペクト比を変化させると、横の視界だけが変化するのがよくわかります）
![](/images/2022/07/16/05.gif)

## Unityのカメラを水平画角固定に変える

垂直画角と画面のアスペクト比が与えられた時、水平画角は以下の式で求められます。

$$(水平画角) = 2 * \arctan(\frac{\tan{\frac{(垂直画角)}{2}}}{(アスペクト比)})$$

この式を利用すれば、Unityのカメラにおいて水平画角を固定することが可能です。

以下のスクリプトで作られるコンポーネントをCameraオブジェクトにアタッチすれば、水平画角を固定できます！

```cs
[ExecuteInEditMode]
public class CameraAspectController : MonoBehaviour
{
    private Camera cam;
    [SerializeField] private float fieldOfViewDegree;

    private enum FOVMode
    {
        Vertical,
        Horizontal
    };

    [SerializeField] private FOVMode fovMode;

    private void Awake()
    {
        cam = GetComponent<Camera>();
    }

    private void FixedUpdate()
    {
        float currentAspectRatio = cam.aspect;
        float verticalFov = 0;
        switch (fovMode)
        {
            case FOVMode.Vertical: // this.fieldOfViewDegreeが垂直画角
                verticalFov = fieldOfViewDegree;
                break;
            case FOVMode.Horizontal: // this.fieldOfViewDegreeが水平画角
                verticalFov = HorizontalToVerticalFov(fieldOfViewDegree, currentAspectRatio);
                break;
        }

        cam.fieldOfView = verticalFov;
    }

    private float HorizontalToVerticalFov(float horizontalFov, float aspectRatio)
    {
        return 2f * Mathf.Rad2Deg * Mathf.Atan(Mathf.Tan(horizontalFov * 0.5f * Mathf.Deg2Rad) / aspectRatio);
    }

    private float VerticalToHorizontalFov(float verticalFov, float aspectRatio)
    {
        return 2f * Mathf.Rad2Deg * Mathf.Atan(Mathf.Tan(verticalFov * 0.5f * Mathf.Deg2Rad) * aspectRatio);
    }
}
```

![](/images/2022/07/16/04.gif)

確かにアスペクト比にかかわらず横の視界を固定することができました。

## 参考

[横方向の視界を揃えて公平なゲームを作るぞ、という話 - Qiita](https://qiita.com/flankids/items/c59acc2d1901dbecca72)
