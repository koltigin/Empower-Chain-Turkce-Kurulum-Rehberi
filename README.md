# Empower Chain circulus-1 Testnet Kurulum Rehberi

![Empow-GitHub](https://github.com/koltigin/Empower-Chain-Turkce-Kurulum-Rehberi/assets/102043225/59feaac2-92ea-4fac-a313-928552756b6f)

## Bağlantılar
 ✔️ [Empower Website](https://empower.eco)<br>
 ✔️ [EmpowerChain Website](https://empowerchain.io)<br>
 ✔️ [Blockchain Explorer](https://empower.explorers.guru/)<br>
 ✔️ [Doküman](https://docs.empowerchain.io)<br>
 ✔️ [Discord](https://discord.gg/fpdHVZRqqm)<br> 
 ✔️ [Görevler ve Ödüller](https://docs.empowerchain.io/testnet/tasks-and-rewards)

## Gereksinimler 
| Bileşenler | Minimum Gereksinimler | **Tavsiye Edilen Gereksinimler** | 
| ------------ | ------------ | ------------ |
| CPU |	4 | 4 |
| RAM	| 8 GB | 16 GB |
| Storage	| 250 GB SSD | 500 GB+ SSD |

## Ödüller
Tüm ödüller linear olarak 1 yıl süreyle verilecektir. 
Ödüller ve görevler ile ilgili olarak resmi dökummana [buradan](https://docs.empowerchain.io/testnet/overview) erişebilirsiniz.

## Test Aşamayaları

**31 Mayıs - 6 Haziran: Ağ önyükleme aşaması:** bu yalnızca doğrulayıcılar için geçerlidir.
**7 Haziran - 20 Haziran: Ana aşama:** bu herkes içindir (testçiler, geliştiriciler, doğrulayıcılar)
**21 Haziran - 25 Haziran: Stres testi aşaması:** Bu aynı zamanda herkes içindir, ancak ağa spam göndermeye odaklanılacaktır.

**28 Haziran:** Mainnet lansmanı için hedef tarihtir.

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
ver="1.20"
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
PEERS="b897014f22e932e461e7fc98353a57d642dbe16e@empower-testnet.nodejumper.io:32656,0e56c10d30f4f16f5be4146bb905471ba99051df@86.48.17.208:15056,95ea7999e3ecd3fb7fd73fae70b3b29a6af24c8d@46.4.5.45:17456,a8f7749ee8ba55b5c2181a1591d7e291db594883@65.108.230.113:22056,42e60e3b5784c1f670450376a095e221e4b0edc3@157.90.152.90:15056,ac877ec4de91c481ea4f09791d730d73a2434d43@65.109.230.178:15056,1af395b39f5d4bac328e0838f1eb026c640e9915@5.189.166.120:15056,304a2af3db879b52a8baf571e6ec7ff1b13de93c@65.108.250.118:15056,d5ea6abacb19ade1e76f326a2b6d2ff2ce41ef51@85.10.192.146:17456,c89be50ba68fafe691b15e8833988cb79297dab2@185.209.230.127:15056,2bcbfbe6d9a1677d16e4e745066ea84aa3af0731@162.55.234.70:54856,c413d3d16e250ddbd8f8d495204b2de46ef36b63@65.109.92.79:16656,1d9a92feae6c392ca218b07175a602c996cd28b6@109.236.82.5:22056,8a2dd7c4d01ff6a78e05734a938097b01f0df8c8@84.39.241.20:36656,8660a1220360f861a8402b49b9904409b0bf3e5f@178.18.253.235:26656,98e9b2f08110e5114a1c159ee6b9022f48b6748b@185.232.69.114:15056,b454e13b623822aa8acd2e72ffc5729e11e24f9e@149.102.135.131:15056,c1caa84f93f9e3bae30f51889ef6f4b588262fcf@65.108.72.233:42656,b900bd530ecd1a84998c292e65fdcda1cdfe64ce@88.99.3.158:17456,999a4f97f429424fc8f91d4c976cb85a5dea58ae@135.181.216.54:3020,cbb420a9a577c14da4585d4536de2d4d6e07a47c@45.67.217.22:16656,f95be62dddc6c3d962e229a19a8384892b2a2f64@95.217.160.106:15056,0cc4a2f06809e082f40b10405d9af701e48499e8@207.180.224.21:15056,5ae9c93114929b2d54f9ab4f128b8bcb22afe0ea@159.69.240.154:15056,2b841f7afc69f0a1b4ade5719466d7e1c3db4d64@158.220.110.46:15056,aa71afed8b1a0eff3b1ef90ba9a3645e5bfbdc2b@134.122.76.244:35656,52244a4389c2df68459f5bc270d64d842f0334f6@104.248.242.212:656,c075bade15eeade5d7b7f1e1949ab454b56fe0c9@85.190.246.173:15056,a5d016db9e0e905a2ab82e2d04c5f181ca887467@167.235.154.9:26656,726523096c6e0f8fd1bd4768791b5bada08188a5@195.3.222.188:26656,6e22654327fd358289478d50b3403f5f0f89cb2d@213.133.100.172:27322,8aa25cf684f2bf6d80fa78101bff855a8bd0e21a@37.27.8.22:26656,2440db5de9c43b8df29788f7a23f00c2264d5e01@178.63.102.172:21656,47746f294c55187bcb68f69d6123b054ea05b4d7@89.117.57.140:26656,85d73f6d3d00336fc187a1d9e9627ebd1f4cc92e@142.132.208.26:26656,9d1b59b9773bd9275115d195ac5c59dc550fa346@65.109.146.243:27656,8cd17dfb379be4b2de810ca98b8af541d699dc1f@5.161.184.204:26656,6ea2ceefef12d0c4a35428d73b3e6b2ea245cf67@34.69.166.27:26656,380d0bc2801189e5f598286c18aebe9c9edfcdae@116.203.110.217:26656,2e7642d44840f8f847ea9247f65dbd2448b1f9e9@65.21.63.110:15056,2b769366ec24fea0bd4cbf70487bc3dddfa1920b@95.216.215.68:15056,0068ef219d7459688c5c87ec2a4f9c8560797227@78.46.175.17:26656,46f76c13d17fa3821786051223e2a3fcadad7b98@128.140.13.139:26656,95f167362aa7a63ef5f53163fb190854a275c8d6@168.119.64.20:17456,00108cfcf7295543131f7af9d3a83fa9d5a59ce7@65.108.124.219:46656,8f28729a4bdbf0710246b557a0bb110c07968ff7@65.109.89.18:16656,cba11214b0e50479ea1bf06c939f11a2fd56f080@149.102.154.1:26656,da334a6aa812e11dc6139e81f36eaff102ced7fe@65.108.149.50:15056,e855b3e36e9506695ffc39a180af8c0453928b7c@89.117.56.126:24356,297c1a4bd58bfe3a7e029e1ad627463ea233c090@2.58.82.21:16656,2304a3a885a1c2ced7453a79c445979f425b821d@65.108.199.206:32656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.empowerchain/config/config.toml
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
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${EMPOW_PORT}657\"%" $HOME/.empowerchain/config/client.toml
```

## Min. GAS Ayarlama
```bash
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.001umpwr"|g' $HOME/.empowerchain/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.empowerchain/config/config.toml
```

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
**Kaynak: [NodeJumper](https://nodejumper.io/empower-testnet/sync)**

## Servisi Başlatma ve Logları Kontrol Etme
```bash
systemctl daemon-reload && \
systemctl enable empowerd && \
systemctl restart empowerd
```

## Logları Kontrol Etme
```shell
journalctl -u empowerd -f -o cat
```  

## Cüzdan Oluşturma

### Yeni Cüzdan Oluşturma
`$EMPOW_WALLET` bölümünü değiştirmiyoruz kurulumun başında cüzdanımıza değişkenler ile isim belirledik.
```bash
empowerd keys add $EMPOW_WALLET
```

### Var Olan Cüzdanı İçeri Aktarma
```shell
empowerd keys add $EMPOW_WALLET --recover
```

## Cüzdan ve Valoper Bilgileri
Burada cüzdan ve valoper bilgilerimizi değişkene ekliyoruz.

```shell
EMPOW_WALLET_ADDRESS=$(empowerd keys show $EMPOW_WALLET -a)
EMPOW_VALOPER_ADDRESS=$(empowerd keys show $EMPOW_WALLET --bech val -a)
```

```shell
echo 'export EMPOW_WALLET_ADDRESS='${EMPOW_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EMPOW_VALOPER_ADDRESS='${EMPOW_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

🔴 **Cüzdanınızın mnemonic kelimelerini kaydetmeyi unutmayınız!**

## Faucet
Discord [#faucet](https://discord.gg/fpdHVZRqqm) kanalından `$request CUZDAN-ADRESINIZ altruistic-1
` şeklinde mesaj atarak token isteyebilirsiniz. 24 saatte bir token isteme hakkınız bulunuyor.
🔴 **Faucet açıldıktan sonra validator oluşturacağız.**

🔴 **BU AŞAMADAN SONRA NODE'UMUZUN EŞLEŞMESİNİ BEKLİYORUZ.**

## Senkronizasyonu Kontrol Etme
`false` çıktısı almadıkça bir sonraki yani validator oluşturma adımına geçmiyoruz.
```shell
empowerd status 2>&1 | jq .SyncInfo
```

🔴 **Eşleşme tamamlandıysa aşağıdaki adıma geçiyoruz.**

## Validator Oluşturma
 Aşağıdaki komutta aşağıda berlittiğim yerler dışında bir değişikli yapmanız gerekmez;
   - `identity`  burada `XXXX1111XXXX1111` yazan yere `https://keybase.io` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
   - `details` `Always forward with the Anatolian Team 🚀` yazan yere kendiniz hakkında bilgiler yazabilirsiniz.
   - `website`  `https://anatolianteam.com` yazan yere varsa bir siteniz ya da twitter vb. adresinizi yazabilirsiniz.
   - `security-contact`  E-posta adresiniz.

 ```shell 
empowerd tx staking create-validator \
--amount=9900000umpwr \
--pubkey=$(empowerd tendermint show-validator) \
--moniker=$EMPOW_NODENAME \
--chain-id=$EMPOW_CHAIN_ID \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation="1" \
--gas-prices=0.1umpwr \
--gas-adjustment=1.5 \
--gas=auto \
--from=$EMPOW_WALLET \
--details="Always forward with the Anatolian Team 🚀" \
--security-contact="xxxxxxx@gmail.com" \
--website="https://anatolianteam.com" \
--identity="XXXX1111XXXX1111" \
--yes
``` 
 
 ## Explorer
 [EmpowerChain](https://empowerchain.exploreme.pro/)
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
empowerd keys delete $EMPOW_WALLET
```

### Cüzdan Bakiyesine Bakma
```shell
empowerd query bank balances $EMPOW_WALLET_ADDRESS
```

### Bir Cüzdandan Diğer Bir Cüzdana Transfer Yapma
```shell
empowerd tx bank send $EMPOW_WALLET_ADDRESS GONDERILECEK_CUZDAN_ADRESI 100000000ubpf
```

### Proposal Oylamasına Katılma
```shell
empowerd tx gov vote 1 yes --from $EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID 
```

### Validatore Stake Etme / Delegate Etme
```shell
empowerd tx staking delegate $VALOPER_ADDRESS 100000000umpwr --from=$EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID  --gas-prices=0.1umpwr --gas-adjustment=1.5 --gas=auto
```

### Mevcut Validatorden Diğer Validatore Stake Etme  Redelegate Etme
```shell
empowerd tx staking redelegate MevcutValidatorAdresi StakeEdilecekYeniValidatorAdresi 100000000ubpf --from=$EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID --gas-prices=0.1umpwr --gas-adjustment=1.5 --gas=auto
```

### Ödülleri Çekme
```shell
empowerd tx distribution withdraw-all-rewards --from=$EMPOW_WALLET --chain-id=$EMPOW_CHAIN_ID --gas-prices=0.1umpwr --gas-adjustment=1.5 --gas=auto
```

```shell
empowerd tx distribution withdraw-rewards $EMPOW_VALOPER_ADDRESS --from=$EMPOW_WALLET --commission --chain-id=$EMPOW_CHAIN_ID --gas-prices=0.1umpwr --gas-adjustment=1.5 --gas=auto
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
systemctl stop empowerd && \
systemctl disable empowerd && \
rm /etc/systemd/system/empowerd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm .empowerchain -rf && \
rm empowerchain -rf && \
rm $(which empowerd) -rf 
sed -i '/EMPOW_/d' ~/.bash_profile
```

# Hesaplar

[Anatolian Team](https://anatolianteam.com)

[Twitter](https://twitter.commehmetkoltigin)

[Medium](https://medium.com/@mehmetkoltigin)

[YouTube](https://www.youtube.com/channel/UCmLgaftx5e38BE0E7gpY2dA)

[Discord](https://discordapp.com/users/837933958280904737)

[Telegram](https://t.me/mehmetkoltigin)
