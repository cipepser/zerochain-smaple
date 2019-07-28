# zerochain-smaple

[Introduction \- Zerochain Book](https://layerxcom.github.io/zerochain-book/ch00-00-introduction.html)を写経してzerochainに触ってみる。

## 1. Getting Started

### Confidential payment

依存のインストールとzerochainのbuild。

```bash
rustup update stable
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
cargo +nightly install --git https://github.com/alexcrichton/wasm-gc

brew install cmake pkg-config openssl git llvm

git clone git@github.com:LayerXcom/zero-chain.git
cd zero-chain
./build.sh
cargo build --release

cargo install --force --path zface
```

walletの初期化

```bash
zface wallet init
# ここのzfaceが足りない(typo)

zface0728
```

```bash
❯ zface wallet init
Set a wallet password. This is for local usage only, allows you to protect your cached private key and prevent from creating non desired transactions.
wallet password: [hidden]
Please, note carefully the following mnemonic words. They will be needed to recover your wallet.
artist toss summer account gravity economy blanket wrap close useless term trip
Enter a new account name.
new account name: z1
wallet and a new account successfully created.

        z1: 5GxBpmdPgsJ584pym1C4FMm2J6xHno72jLcutJELt3bPRjqc
```

zerochainの起動。

```bash
./target/release/zerochain --dev
```

4.1に`of couse`のtypoがあった。

zerochainは起動したまま別ターミナルで以下を実行。

```bash
❯ zface debug send -t 5GxBpmdPgsJ584pym1C4FMm2J6xHno72jLcutJELt3bPRjqc -a 100

Preparing paramters...
Computing zk proof...
Start submitting a transaction to Zerochain...
Submitted transaction; Waiting response from Zerochain...
Finalized extrinsic hash: 0x74bf39cc1d1000e63b61e99bb4dafbcffa57a692d204069baf639e93a5b049f6
Remaining balance is 9899
```

これで自分のwalletを確認すると残高が増えている。

```bash
❯ zface wallet balance

Getting encrypted balance from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 100
Encrypted balance: 0x00
Encrypted pending transfer: 0xf349422245382e91c719463073acda99132cbb4cdc590f3312062b79b00d6bbd2ce953e4e8b3d4e03214b17c5e3aabe36b66a7d30bb134ccb43a5e8944fd3ea0
```

アカウントを更に追加する。

```bash
❯ zface wallet add-account

Enter the wallet password.
wallet password: [hidden]
Enter a new account name.
new account name: z2
a new account successfully created.

        z2: 5EdTz2yX3qrzCX3fVFfRvZxsGWLKk8DwL7u5Ya5bdx2sMNCr
```

アカウントの切り替え。

```bash
❯ zface wallet change-account -n z2
```

当然残高は0。

```bash
❯ zface wallet balance

Getting encrypted balance from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 0
Encrypted balance: 0x00
Encrypted pending transfer: 0x00
```

アカウント一覧。

```bash
❯ zface wallet list

z1: 5GxBpmdPgsJ584pym1C4FMm2J6xHno72jLcutJELt3bPRjqc
* z2: 5EdTz2yX3qrzCX3fVFfRvZxsGWLKk8DwL7u5Ya5bdx2sMNCr
```

## References
- [Introduction \- Zerochain Book](https://layerxcom.github.io/zerochain-book/ch00-00-introduction.html)