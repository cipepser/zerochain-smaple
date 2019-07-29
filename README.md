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

### Encrypted fungible assets

アセットを作る。

```bash
❯ zface tx asset-issue -a 100

Enter the wallet passowrd.
wallet password: [hidden]
Preparing paramters...
Computing zk proof...
Start submitting a transaction to Zerochain...
Submitted transaction; Waiting response from Zerochain...
Submitting transaction is completed successfully.
The total issued coin is 100.
The asset id is 0.
Finalized extrinsic hash: 0x0f368476e762e175ccb44f03731f31a23dd6aa0f114f5af56857c1468085c8fe
```

残高を確認すると増えている。

```bash
❯ zface wallet asset-balance -i 0

Getting encrypted asset from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 100
Encrypted balance: 0x4d354bea4a7a43d1c022bb3529c2cf6444acd3d10c8bfbb5ff79649fcde215af2ded195625bd39fb422079bda07495f095f536f14424d5c15687b24f0ef3d99d
Encrypted pending transfer: 0x00
```

アカウント切り替え。

```bash
❯ zface wallet change-account -n z1

❯ zface wallet list
* z1: 5GxBpmdPgsJ584pym1C4FMm2J6xHno72jLcutJELt3bPRjqc
z2: 5EdTz2yX3qrzCX3fVFfRvZxsGWLKk8DwL7u5Ya5bdx2sMNCr
```

こっちは残高0。

```bash
❯ zface wallet asset-balance -i 0

Getting encrypted asset from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 0
Encrypted balance: 0x00
Encrypted pending transfer: 0x00
```

`z2`から`z1`にアセットを`20`送ってみる。

```bash
❯ zface tx asset-transfer -a 20 -i 0 -t 5GxBpmdPgsJ584pym1C4FMm2J6xHno72jLcutJELt3bPRjqc
Enter the wallet passowrd.
wallet password: [hidden]
Preparing paramters...
Computing zk proof...
Start submitting a transaction to Zerochain...
Submitted transaction; Waiting response from Zerochain...
Submitting transaction is completed successfully.
Remaining balance is 79.
The asset id is 0.
Finalized extrinsic hash: 0x8f5cc62b8cdad7b2c36d970a5bb2e0264a7259c7f9f0587602919e59721ee752
```

`z2`の残高はfee`1`も差し引いて、`79`になっている。

```bash
❯ zface wallet asset-balance -i 0

Getting encrypted asset from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 79
Encrypted balance: 0x4f2969a6b3f55c62e27327d0306208c3dbb293dee3e357c624d7db1d0b8c5d0031dc53dfbeabee6c22702328aa1a2f77ae7e7ddde29a6316a53f55fc6e0be386
Encrypted pending transfer: 0x00
```

アカウントを切り替えるとこっちは`20`

```bash
❯ zface wallet asset-balance -i 0

Getting encrypted asset from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 20
Encrypted balance: 0x00
Encrypted pending transfer: 0x2f8b8ed281096e16255aeb3a492d8a466fba0c7cf2b499f577382b527fdb8e4cc4c97e70cbacf142a18539c9363eaf92667db9178cf113e1ca8ff8eee2ada9e5
```

burnしてみる。

```bash
❯ zface tx asset-burn -i 0

Enter the wallet passowrd.
wallet password: [hidden]
Preparing paramters...
Computing zk proof...
Start submitting a transaction to Zerochain...
Submitted transaction; Waiting response from Zerochain...
destroyed coins.
The asset id is 0.
Finalized extrinsic hash: 0x04f3a28fea974d68e4e6308408e31e19a2a5b290e6e6881e156c57a461db8364
```

burnしたので残高がなくなっている。

```bash
❯ zface wallet asset-balance -i 0

Getting encrypted asset from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 0
Encrypted balance: 0x00
Encrypted pending transfer: 0x00
```

burnされるのは対象のアカウントのみ。`z2`には`79`のまま残っている。

```bash
❯ zface wallet asset-balance -i 0

Getting encrypted asset from zerochain
Enter the wallet password.
wallet password: [hidden]
Decrypted balance: 79
Encrypted balance: 0x4f2969a6b3f55c62e27327d0306208c3dbb293dee3e357c624d7db1d0b8c5d0031dc53dfbeabee6c22702328aa1a2f77ae7e7ddde29a6316a53f55fc6e0be386
Encrypted pending transfer: 0x00
```

## 3. Confidential Payment

- 3.2.の`conf_transfer module`のリンクが切れている。
- Pending transferの`alice`、`bob`（大文字が小文字になっている）

`confidential_transfer`は内部で3つの機能を呼ぶ。
- `roll_over`: 非同期にtxを送る。フロントランニング防止。
- `validate_proof`: zk proofsのvalidation
- `?`: 暗号化されたストレージ上のデータを変更する

## References
- [Introduction \- Zerochain Book](https://layerxcom.github.io/zerochain-book/ch00-00-introduction.html)