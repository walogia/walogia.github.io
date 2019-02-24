# SlimxDx 环境搭建

[详见](https://qiita.com/JI1LDG/items/9de611bce86c0b3d5b6f)
[代码](https://github.com/JI1LDG/ModelViewer/tree/9da064ea406971badc5e718cede70f9dfaf12f53)

## 第一步

使用Nuget，添加SlimDX(DirectX11)

## 第二步

"Core.cs"を追加し、以下のコードを記述する。
プロジェクトから"form1.cs"を削除し、以下のように"Program.cs"を書き換える

## 第三步

Core.cs
~~~
using SlimDX.Windows;
using System.Windows.Forms;

namespace ModelViewer {
    class Core : Form {
        public void Run() {
            MessagePump.Run(this, Draw);
        }

        protected virtual void Draw() {
            //描画処理
        }
    }
}
~~~

Program.cs
~~~
namespace ModelViewer {
    static class Program {
        static void Main() {
            using(var core = new Core()) {
                core.Run();
            }
        }
    }
}
~~~

## 第四步

为了让背景变成蓝色

次に、DirectXで3DCGの処理・表示を行うためにデバイスの初期化を行います。
また、背景色を設定し、DirectXが動いているか確認をします。

CreateWithSwapChain()を使用して、3DCGの処理を行うデバイスと、ウィンドウに画像を表示するためのスワップチェーンを生成します。
描画対象を表すRenderTargetViewを生成し、青一色で初期化する。 Core.csを以下のように書き換えます。

Core.cs
~~~
using SlimDX;
using SlimDX.Windows;
using System.Windows.Forms;
using Dx11 = SlimDX.Direct3D11;
using Dxgi = SlimDX.DXGI;

namespace ModelViewer {
    class Core : Form {
        private Dx11.Device device;
        private Dxgi.SwapChain swapChain;
        private Dx11.RenderTargetView renderTarget;

        public void Run() {
            InitializeDevice();
            MessagePump.Run(this, Draw);
            DisposeDevice();
        }

        protected virtual void Draw() {
            device.ImmediateContext.ClearRenderTargetView(renderTarget, new Color4(1, 0, 0, 1));

            swapChain.Present(0, Dxgi.PresentFlags.None);
        }

        private void InitializeDevice() {
            Dx11.Device.CreateWithSwapChain(
                Dx11.DriverType.Hardware, Dx11.DeviceCreationFlags.None,
                new Dxgi.SwapChainDescription() {
                    BufferCount = 1, OutputHandle = this.Handle,
                    IsWindowed = true,
                    SampleDescription = new Dxgi.SampleDescription() {
                        Count = 1, Quality = 0,
                    }, ModeDescription = new Dxgi.ModeDescription() {
                        Width = ClientSize.Width, Height = ClientSize.Height,
                        RefreshRate = new Rational(60, 1),
                        Format = Dxgi.Format.R8G8B8A8_UNorm
                    }, Usage = Dxgi.Usage.RenderTargetOutput
                }, out device, out swapChain
            );

            InitializeRenderTarget();
        }

        private void InitializeRenderTarget() {
            using(var backBuffer = Dx11.Resource.FromSwapChain<Dx11.Texture2D>(swapChain, 0)) {
                renderTarget = new Dx11.RenderTargetView(device, backBuffer);
            }
        }

        private void DisposeDevice() {
            renderTarget.Dispose();
            device.Dispose();
            swapChain.Dispose();
        }
    }
}
~~~