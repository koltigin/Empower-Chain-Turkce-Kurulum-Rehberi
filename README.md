# Empower Chain circulus-1 Testnet Kurulum Rehberi

![empower](https://user-images.githubusercontent.com/104348282/192093493-67779857-653e-4018-8c78-49530690f7a0.png)

## Sistem Gereksinimleri (Minimum)
  * 4 CPU
  * 8GB RAM
  * 200 GB SSD

## Sistemi Güncelleme
**Update packages and install required packages**
```bash
sudo apt update && sudo apt upgrade -y
```

## Gerekli Kütüphanelerin Kurulması
```bash
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## Go Kurulumu
```bash
ver="1.18.4"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
rm -rf /usr/local/go
tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm -rf "go$ver.linux-amd64.tar.gz"
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

Yukarıdaki son kodun çıktısı aşağıdaki gibiyse işlem tamamdır.
  * `go version go1.18.4 linux/amd64`

## Değişkenleri Yükleme
aşağıda değiştirmeniz gereken yerleri yazıyorum.
* `$EMPOW_NODENAME` validator adınız
* `$EMPOW_WALLET` cüzdan adınız
*  Eğer portu başka bir node kullanıyorsa aşağıda `EMPOW_PORT` bömlümündeki port numarasını değiştirebilirsiniz.
```shell
echo "export EMPOW_NODENAME=$EMPOW_NODENAME"  >> $HOME/.bash_profile
echo "export EMPOW_WALLET=$EMPOW_WALLET" >> $HOME/.bash_profile
echo "export EMPOW_PORT=18" >> $HOME/.bash_profile
echo "export EMPOW_CHAIN_ID=circulus-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Örnek
Node ve Cüzdan adımızın `Mehmet` olduğunu varsayalım. Kod aşağıdaki şekilde düzenlenecektir. 
```shell
echo "export EMPOW_NODENAME=Mehmet"  >> $HOME/.bash_profile
echo "export EMPOW_WALLET=Mehmet" >> $HOME/.bash_profile
echo "export EMPOW_PORT=18" >> $HOME/.bash_profile
echo "export EMPOW_CHAIN_ID=circulus-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Empower Chain Kurulumu
```bash
git clone https://github.com/EmpowerPlastic/empowerchain
cd empowerchain/chain || return
git checkout v1.0.0-rc1
make install
empowerd version
`````
Version çıktısı `1.0.0-rc1` olacak.

## Uygulamayı Yapılandırma
```bash
empowerd config keyring-backend test
empowerd init $EMPOW_NODENAME --chain-id $EMPOW_CHAIN_ID && \
empowerd config chain-id $EMPOW_CHAIN_ID
```

## Genesis ve Addrbook Dosyasını İndirme
```bash
curl -s https://raw.githubusercontent.com/EmpowerPlastic/empowerchain/main/testnets/circulus-1/genesis.json > $HOME/.empowerchain/config/genesis.json
curl -s https://snapshots2-testnet.nodejumper.io/empower-testnet/addrbook.json > $HOME/.empowerchain/config/addrbook.json
```

## PEERS Ekleme
```bash
SEEDS="258f523c96efde50d5fe0a9faeea8a3e83be22ca@seed.circulus-1.empower.aviaone.com:20272,d6a7cd9fa2bafc0087cb606de1d6d71216695c25@51.159.161.174:26656,babc3f3f7804933265ec9c40ad94f4da8e9e0017@testnet-seed.rhinostake.com:17456"
PEERS=""
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.empowerchain/config/config.toml
```

## Indexer'i Kapatma (Opsiyonel)
```bash
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.empowerchain/config/config.toml
```

## Prometheus'u Aktif Etme
```bash
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.empowerchain/config/config.toml
```

## Pruning'i Ayarlama (Opsiyonel)
```bash
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.empowerchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.empowerchain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.empowerchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.empowerchain/config/app.toml
```

## Portları Ayarlama
🔴 **Port değişikliği yapmadıysanız bu adımı geçebilirsiniz**
```bash
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${EMPOW_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${EMPOW_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${EMPOW_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${EMPOW_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${EMPOW_PORT}660\"%" $HOME/.empowerchain/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${EMPOW_PORT}317\"%; s%^address = \":8080\"%address = \":${EMPOW_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${EMPOW_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${EMPOW_PORT}091\"%" $HOME/.empowerchain/config/app.toml
```

## Cüzdan Oluşturma

`$EMPOW_WALLET` bölümünü değiştirmiyoruz kurulumun başında cüzdanımıza değişkenler ile isim belirledik.
```bash
empowerd keys add $EMPOW_WALLET
```
🔴 **Cüzdanınızın mnemonic kelimelerini kaydetmeyi unutmayınız!**

## Servis Dosyası Oluşturma
```bash
sudo tee /etc/systemd/system/empowerd.service > /dev/null <<EOF
[Unit]
Description=EmpowerChain Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which empowerd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Snaphot İndirme (Nodejumper)
```bash
empowerd tendermint unsafe-reset-all --home $HOME/.empowerchain --keep-addr-book
SNAP_NAME=$(curl -s https://snapshots2-testnet.nodejumper.io/empower-testnet/info.json | jq -r .fileName)
curl "https://snapshots2-testnet.nodejumper.io/empower-testnet/${SNAP_NAME}" | lz4 -dc - | tar -xf - -C "$HOME/.empowerchain"
```

## Servisi Başlatma ve Logları Kontrol Etme
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable empowerd && \
sudo systemctl restart empowerd && \
sudo journalctl -u empowerd -f -o cat
```

## Bağlanmada Sorun Yaşarsanız ya da Snap Kurulumu Yapmak İsterseniz

```bash
sudo apt update
sudo apt install lz4 -y

sudo systemctl stop empowerd

cp $HOME/.empowerchain/data/priv_validator_state.json $HOME/.empowerchain/priv_validator_state.json.backup
empowerd tendermint unsafe-reset-all --home $HOME/.empowerchain --keep-addr-book

rm -rf $HOME/.empowerchain/data 

SNAP_NAME=$(curl -s https://snapshots2-testnet.nodejumper.io/empower-testnet/ | egrep -o ">altruistic-1.*\.tar.lz4" | tr -d ">")
curl https://snapshots2-testnet.nodejumper.io/empower-testnet/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.empowerchain

mv $HOME/.empowerchain/priv_validator_state.json.backup $HOME/.empowerchain/data/priv_validator_state.json

sudo systemctl restart empowerd
sudo journalctl -u empowerd -f --no-hostname -o cat
```

**Kaynak: [NodeJumper](https://nodejumper.io/empower-testnet/sync)**

## Faucet
Discord [#faucet](https://discord.gg/BUwMw5JUqS) kanalından `$request CUZDAN-ADRESINIZ altruistic-1
` şeklinde mesaj atarak token isteyebilirsiniz. 24 saatte bir token isteme hakkınız bulunuyor.
🔴 **Faucet açıldıktan sonra validator oluşturacağız.**


## Validator Oluşturma
 Aşağıdaki komutta aşağıda berlittiğim yerler dışında bir değişikli yapmanız gerekmez;
   - `identity`  burada `XXXX1111XXXX1111` yazan yere `httpskeybase.io` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
   - `details` `Always forward with the Anatolian Team 🚀` yazan yere kendiniz hakkında bilgiler yazabilirsiniz.
   - `website`  `https://anatolianteam.com` yazan yere varsa bir siteniz ya da twitter vb. adresinizi yazabilirsiniz.
   - `security-contact`  E-posta adresiniz.
```shell 
empowerd tx staking create-validator \
--amount=9900000umpwr \
--pubkey=$(empowerd tendermint show-validator) \
--moniker=$EMPOW_NODENAME \
--chain-id=$EMPOW_CHAIN_ID \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--fees=250umpwr \
--gas=200000 \
--from=$EMPOW_WALLET \
--details="Always forward with the Anatolian Team 🚀" \
--security-contact="xxxxxxx@gmail.com" \
--website="https://anatolianteam.com" \
--identity="XXXX1111XXXX1111" \
-y
 ```  
 
 ## Explorer
 [Zendesk](https://testnet-empower.zenscan.io/validators.php)
 [Nodes Guru](https://empower.explorers.guru)

🟡 `config` dosyası içerisinde ki `priv_validator_key.json` dosyasını yedek almayı unutmayınız!

## FAYDALI KOMUTLAR

### Logları Kontrol Etme 
```shell
journalctl -fu empowerdd -o cat
```

### Sistemi Başlatma
```shell
systemctl start empowerdd
```

### Sistemi Durdurma
```shell
systemctl stop empowerdd
```

### Sistemi Yeniden Başlatma
```shell
systemctl restart empowerdd
```

### Node Senkronizasyon Durumu
```shell
empowerd status 2>&1 | jq .SyncInfo
```

### Validator Bilgileri
```shell
empowerd status 2>&1 | jq .ValidatorInfo
```

### Node Bilgileri
```shell
empowerd status 2>&1 | jq .NodeInfo
```

### Node ID Öğrenme
```shell
empowerd tendermint show-node-id
```

### Node IP Adresini Öğrenme
```shell
curl icanhazip.com
```

### Peer Adresinizi Öğrenme
```shell
echo $(empowerd tendermint show-node-id)@$(curl ifconfig.me):18656
```

### Cüzdanların Listesine Bakma
```shell
empowerd keys list
```

### Cüzdanı İçeri Aktarma
```shell
empowerd keys add $EMPOW_WALLET --recover
```

### Cüzdanı Silme
```shell
empowerd keys delete CUZDAN_ADI
```

### Cüzdan Bakiyesine Bakma
```shell
empowerd query bank balances CUZDAN_ADRESI
```

### Bir Cüzdandan Diğer Bir Cüzdana Transfer Yapma
```shell
empowerd tx bank send CUZDAN_ADRESI GONDERILECEK_CUZDAN_ADRESI 100000000ubpf
```

### Proposal Oylamasına Katılma
```shell
empowerd tx gov vote 1 yes --from $EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID 
```

### Validatore Stake Etme  Delegate Etme
```shell
empowerd tx staking delegate $VALOPER_ADDRESS 100000000umpwr --from=$EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID  --gas=auto
```

### Mevcut Validatorden Diğer Validatore Stake Etme  Redelegate Etme
```shell
empowerd tx staking redelegate MevcutValidatorAdresi StakeEdilecekYeniValidatorAdresi 100000000ubpf --from=$EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID --gas=auto
```

### Ödülleri Çekme
```shell
empowerd tx distribution withdraw-all-rewards --from=$EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID --gas=auto
```

### Komisyon Ödüllerini Çekme
```shell
empowerd tx distribution withdraw-rewards VALIDATOR_ADRESI --from=$EMPOW_WALLET --commission --chain-id=$EMPOW_CHAIN_ID 
```

### Validator İsmini Değiştirme
```shell
empowerd tx staking edit-validator 
--moniker=YENI_NODE_ADI 
--chain-id=$EMPOW_CHAIN_ID  
--from=$EMPOW_WALLET
```

### Validatoru Jail Durumundan Kurtarma 
```shell
empowerd tx slashing unjail \
  --broadcast-mode=block \
  --from=$EMPOW_WALLET \
  --chain-id=$EMPOW_CHAIN_ID \
  --gas=auto \
  -y
```

### Node'u Tamamen Silme 
```shell
sudo systemctl stop empowerd
sudo rm /etc/systemd/system/empowerd.service -rf
sudo rm $HOME/.empowerchain/ -rf
sudo rm $HOME/empowerchain -rf
sudo rm /usr/local/bin/empowerd -rf
```

# Hesaplar

[Anatolian Team](https://anatolianteam.com)

[Twitter](https://twitter.commehmetkoltigin)

[Medium](https://medium.com/@mehmetkoltigin)

[YouTube](https://www.youtube.com/channel/UCmLgaftx5e38BE0E7gpY2dA)

[Discord](https://discordapp.com/users/837933958280904737)

[Telegram](https://t.me/mehmetkoltigin)
