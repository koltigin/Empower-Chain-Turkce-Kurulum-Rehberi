# Empower Chain altruistic-1 Testnet Kurulum Rehberi

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
echo "export EMPOW_CHAIN_ID=altruistic-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Örnek
Node ve Cüzdan adımızın `Mehmet` olduğunu varsayalım. Kod aşağıdaki şekilde düzenlenecektir. 
```shell
echo "export EMPOW_NODENAME=Mehmet"  >> $HOME/.bash_profile
echo "export EMPOW_WALLET=Mehmet" >> $HOME/.bash_profile
echo "export EMPOW_PORT=18" >> $HOME/.bash_profile
echo "export EMPOW_CHAIN_ID=altruistic-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Empower Chain Kurulumu
```bash
cd $HOME && git clone https://github.com/empowerchain/empowerchain && \
cd empowerchain/chain && \
make install && \
empowerd version --long | head
`````

## Uygulamayı Yapılandırma
```bash
empowerd init $EMPOW_NODENAME --chain-id $EMPOW_CHAIN_ID && \
empowerd config chain-id $EMPOW_CHAIN_ID
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

## Genesis Hesabı Ekleme 
```bash
empowerd add-genesis-account $EMPOW_WALLET 1000000umpwr
```

## Gentx Oluşturma
 Aşağıdaki komutta aşağıda berlittiğim yerler dışında bir değişikli yapmanız gerekmez;
  - `identity`  burayda 'XXXX1111XXXX1111' yazan yere `https://keybase.io` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
  - `details` kendiniz hakkında bilgiler yazabilirsiniz.
  - `website`  Varsa bir siteniz yazabilirsiniz olarak bırakabilirsiniz.
  - `security-contact`  E-posta adresiniz.
```bash
empowerd gentx $EMPOW_WALLET 1000000umpwr \
--chain-id=$EMPOW_CHAIN_ID \
--moniker="$EMPOW_NODENAME" \
--commission-max-change-rate 0.1 \
--commission-max-rate 0.2 \
--commission-rate 0.05 \
--pubkey $(empowerd tendermint show-validator) \
--website="https://anatolianteam.com" \
--security-contact="xxxxxx@gmail.com" \
--identity="XXXX1111XXXX1111" \
--details="Anatolian Team"
```

Aşağıdaki görselde olduğu gibi `gentx-xxxxxxx` şeklinde dosyanız oluşacak.
![image](https://user-images.githubusercontent.com/102043225/194017855-c36e8019-922e-4881-9cd7-411ffd39c603.png)


## Gentx Dosyasını Pull Request Etme
Bir önceki komutu girdikten sonra bir gentx dosyası oluşacak. Bu dosya `/.empowerchain/config/gentx/gentx-xxxxxxxxxxxxxxx.json` şeklinde oluşacak. Sunucunuza winscp, termius gibi bir program ile bağlanıp dosyayı bilgisayarınıza kayedebilirsiniz. Daha sonra sırasıyla aşağıdakileri yapıyoruz
  - 🟡 [EmpowerChain reposunu](https://github.com/empowerchain/empowerchain) forkluyoruz.
  - 🟡 Forkladığımız repoya kendi profilimizden erişip aşağıda resimde görülen `testnets/altruistic-1/gentx` klasörüne giriyoruz ve `Add file` butonuna basıyoruz.  
 ![Ekran Alıntısı-1](https://user-images.githubusercontent.com/102043225/194023060-4794b8bf-8b84-4d0b-a0a6-63da328aff73.JPG)
  - 🟡 Dosyamızın adını aşağıdaki resimde görüldüğü gibi `validator_adiniz-gentx.json` yani şuna benzer `mehmet-gentx.json` şeklinde yazıyoruz.
  - 🟡 Yine aşağıdaki resimde görüldüğü gibi indirdiğimiz gentx dosyasının içeriğini metin kutusuna yapıştırıyoruz.
 ![Ekran Alıntısı-2](https://user-images.githubusercontent.com/102043225/194024246-4581b015-d2c4-458d-ba42-7248414db83e.JPG)
 - 🟡 Daha sonra sayfanın aşağısında bulunan Commit new file butonuna basıyoruz.
 ![Ekran Alıntısı-3](https://user-images.githubusercontent.com/102043225/194024668-4a2417e9-a04a-4887-97ec-451adddc1987.JPG)
 - 🟡 Ardından açıkan sayfada aşağıda görüldüğü gibi `pull requests` sekmesine tıklıyoruz.
 ![Ekran Alıntısı-4](https://user-images.githubusercontent.com/102043225/194041062-879b8e57-c716-46d6-80c0-ef30e26826f5.JPG)
 - 🟡 Açılan sayfada bu sefer `New pull request` butonuna basıyoruz.
 ![Ekran Alıntısı-5](https://user-images.githubusercontent.com/102043225/194041394-5150974d-5de8-4cd6-bfa7-6b67549109f3.JPG)
 - 🟡 Açılan sayfada bu sefer `Create pull request` butonuna basıyoruz.
 ![Ekran Alıntısı-6](https://user-images.githubusercontent.com/102043225/194041795-48c65277-7cd0-485f-bc7e-aa4ea4681631.JPG)
 - 🟡 Açılan ekranda da aşağıdaki resimdeki gibi `Create pull request` butonuna basıyoruz ve işlemi tamamlıyoruz.
 ![Ekran Alıntısı-7](https://user-images.githubusercontent.com/102043225/194042453-4ae3b033-033a-401d-87cf-eb451830f183.jpg)

🟡 `config` dosyası içerisinde ki `priv_validator_key.json` dosyasını yedek almayı unutmayınız!

## altruistic-1 Genesis Dosyasını İndirme
```bash
rm -rf $HOME/.empowerchain/config/genesis.json && cd $HOME/.empowerchain/config && wget https://raw.githubusercontent.com/empowerchain/empowerchain/main/testnets/altruistic-1/genesis.json
```

## Zincir Verilerini Sıfırlama
```bash
empowerd tendermint unsafe-reset-all --home $HOME/.empowerchain
```

## Genesis Dosyasını Kontrol Etme
```bash
sha256sum $HOME/.empowerchain/config/genesis.json
```
Çıktı aşağıdaki gibi olmalıdır
  `fcae4a283488be14181fdc55f46705d9e11a32f8e3e8e25da5374914915d5ca8`
![Ekran Alıntısı-8](https://user-images.githubusercontent.com/102043225/194043211-3834b614-0757-4d72-bf68-9a578291dda5.jpg)

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

## PEERS Ekleme
```bash
seeds=""
peers="ca8b9d5fecd3258cb8bb4164017114898cd63ad5@empower-testnet.nodejumper.io:31656,46b552c62df0523a2bfff285eb384e4b197484aa@65.21.133.125:33656,0314ad645ce08401d190521643503c803e9335dd@65.108.127.215:26696,c3ade1fb182d5cb6a4ed56ce05b84b872c5b5b3e@65.108.229.225:54656,ab4b4331d161cf0e98d3244e30225e4f38ac8d2f@65.109.28.177:44656,e600a7ee92aa824412cc075a3cfdc6318565e333@65.21.131.215:26656,d9307a7ba665a54e65f4fa5dbb5401448e1c3456@65.109.30.117:30656,3b41eaba864fa4d10b00685e6bad4318700b2583@65.108.124.54:54656,9a6b638b3a6ae693c6e51208da832718c35f8f39@65.108.234.126:38656,408980a63332b230a90ad549e93162dab303836f@65.108.225.158:17456,e8f6d75ab37bf4f08c018f306416df1e138fd21c@95.217.135.41:26656,6dae9286b4ef23151148922befc0f32a00cc1ec4@65.21.134.202:26656,3c1b1a4e908c1c2c7fd11961488bedfe1099f0e5@95.217.7.146:46656,c8d488e8ce1dc25dcdb6a420d6406982c46edfe6@135.181.255.131:26656,86669cd5e5914f862578d43de483f49e93d396b1@51.83.35.129:26656,aeb6bdbf28b3883a206570a29cda164e889fc807@194.163.155.84:26656,07e2aac37ac8e337214b53f1fe9121d6d6f238b8@195.201.103.54:26656,a2ed6f01e454d3469a95edee6580e750d79d4297@142.132.199.236:22656,b405572f7bf70f681d1e82f196e1399bf90a9d8a@138.201.197.163:26656,c5d44acd2f0ee122352d2f8154d9b29aeb9bf0ec@159.69.65.97:36656,bd8274637720d5338b31fe143c62e83e938fdeac@194.163.131.83:26676,76537229c17d0d09d4ec672d7c8baa0c0c12e9d2@38.242.246.181:26656,12426ec181f1aef0e7a2aa99a88bde9c357cc8e9@38.242.251.1:26656,7722e5d0f84d72da7bd17aee50184d52999ff7f4@45.84.138.224:26656,ff81518a19e930a7cca34d4a69cabcd2d37c61da@38.242.238.107:26656,8abceaabc650d81a751e40382f80af6c98ba466f@185.239.209.180:35656,f0ac01503193e1f1eb63ee1407726e4da940f17d@161.97.105.150:15656,b5df76282e8704d253012688613d4eb725d3cb12@77.37.176.99:56656,8fabf4b8d1f41530412269493199faaa1e2d2571@80.82.215.243:26666,8498049b61177a53b3f0e6b8f7c4a574251a2bbb@149.102.157.96:36656,409d8644100d1eaa95ac55afb9505060316ba859@5.189.178.222:46656,21787f4466839fd5375914711d83c0ae48305ef8@46.138.245.164:24436,5a68fe7914a170ef3ebbdc404703ac8b5d828bcf@137.184.146.226:26656,326a9806fca6c45ef75c9fcf594cb8f56d6ee63f@5.161.111.18:26656,70406635a08ac7393fd3c9aae53e29f0633a962e@138.197.155.91:26656,56d05d4ae0e1440ad7c68e52cc841c424d59badd@96.234.160.22:26656,50725c195e46b3863ad7de26f044a14941dbe815@193.46.243.171:26656,ff18196236e62eb564cbeb1343eb6a7ecfdfcc77@194.163.175.30:26656,b72e6976e21ccda63c019a956899b374c7fc21fa@149.102.137.255:26656,ed638559942030c6b18a9ebd2bfaab572e9c4f42@65.108.68.233:26756,c2d4fd99f12be0b406cbc4e9416043f39722b4f6@38.242.128.227:26656,afb4e400c98da570d01eb56cc80dbef4b96a5023@135.181.147.143:26656,88b11a53f55079e0849705609eaf4ed9512c461d@65.108.249.220:10656,5935c103703407ba314fe9e20be82c318b99ca58@45.85.146.224:26656,c5f73ce5526e32d1c43eeb1afa29b7928acab9b4@185.169.252.86:26656,707d0e3f757bedaaff6fd92e12c6089b1ebe50d8@194.163.179.16:26656,12917731184e3499a26d614f9542724e2bfcf63a@193.46.243.95:26656,0871e0e36e83453bdea04e93a85e4cace90b4323@139.144.173.65:26656,ec4cbf95e9c2a62b8d9d71a8c8b9b2679dc9d656@167.86.127.111:26656,57191ad7123c24da0a502fb8717e626c9a1750c2@149.102.142.221:26656,99bc732e69ce4d23b75f9188e02eaea9b48be895@68.183.206.107:26656,694c3318816173467221449b17113c607aa497ee@167.99.176.95:26656,cfb9d1b4cfa9a2ba4b0129012498fa5aa4fab63f@178.128.235.188:26656,9fe8cff1e057086c357b6b83b7cf49c2af6c6adb@159.203.41.233:26656,6c523db71cf49a658a747ba952be83de3dd0fc5b@178.79.191.243:26656,f5a5df8a5d168613c2c152a231b9cac88a58be2b@149.102.149.20:26656,671c980d0f0ea6eb5753e2358262ba02a0619eb5@194.247.12.102:26656,cc39a6017d9dc20ff1dab94e93ac8af02962df29@38.242.236.206:26656,f5f2697fef536365789a07d1908417babfbc3464@38.242.251.167:26656,3ea0d5b2d0cdaa7ecd162a3e3e813f181c03eab7@75.119.134.86:26656,7ba7609b37eea967abe94d233ae74245530e8877@135.181.221.186:31656,cbd422c6e9f3eb571d8e10cdcd7fd587711f2538@45.84.138.127:26656,6ad10caf0d0e675482d8fb047d71417d82a7d7ec@173.249.60.132:26656,6fa57fc737fa9d33ecac359f8ca62d84d5fe7a22@149.102.137.217:26656,55f8978c0024c59ad5dcdee0874a9dab2cf916c8@194.180.176.59:26656,42020b7c9520f387bad61cbb2da705d383f38840@95.111.234.147:26656,f44370a859b58f471a8ce9c3ca4584132dbe9717@45.94.209.38:26656,1136f1cad9ce4b248a7619190d08fb24f1635d90@34.82.23.242:26656,f26af8b833e2dc2c890daedfd2dcd2fe3b641ccf@161.97.132.207:26656,2bcbfbe6d9a1677d16e4e745066ea84aa3af0731@162.55.234.70:54856,8721e086acb679c4c48d507096fe0fe532ff70d0@134.122.39.157:26656,596f6892efa7473ea2930ffde0f8c4d60a16eb50@38.242.154.155:26656,3525fd3a8a96a67e150d9a04f493a60f22b2e11a@147.182.246.192:26656,782b69bce37b654ecb1b82232af6269667242447@138.197.167.229:26656,1753538538e03174a637e3dd81f82f6ec7125833@134.122.44.88:26656,7ce890789ed7521399a34427c60f1f9d10edaf93@159.223.167.63:26656,ede8257e4be3d7dc369f652263a139c8c083e97e@149.102.129.159:26656,773a76711d9a99fb3ef1bf72621af547bc77c58e@167.235.50.65:26656,c7eb93b0ad5a3d79ee921cf20fe1aba866d673e6@46.101.183.146:26656,1224ae5aac563920b1fe2e2b496820509db83403@142.93.158.71:26656,f73c4eb3749984a6d5717753fce85fa6b085dc5d@185.182.185.145:26656,8e1c0ff9cd499d6219f24073a63503d1fedbb64b@68.183.38.112:26656,2409fd7723ced295651f913f93f667ed2062bb29@138.68.141.214:10656,ae7ca3688e313aa1d8acf168a2a6efd27a0e9e2e@217.199.117.158:26656,ad19d0fedb825a05a64189bff2b23e617f26eda6@134.122.38.167:26656,8a9559d240c3e89031e1e254c4170f7055f45405@167.86.91.34:26656,ae03dd3ab682d62048808a088bc7621c3baedc22@157.230.183.146:26656,6a3f47a50548cc7a9b91c1446378faab398c7d3b@75.119.146.75:26656,6fcd814ed30f16a516aaef9e089d6de77be3d530@134.122.23.155:26656,76a88e13f1d0f957b93b84375783cfeb11714b67@38.242.146.28:26656,0f42474c058db28cc450d92ff30e127ff16d0b0d@94.130.140.145:26656,bd057ea7df5b109b97d4a7fae2d0e3df244af89b@137.184.178.158:26656,5f31d621b9d348386e1145fdfdeefe0d45cf2030@104.199.121.169:26656,62d27d651195f384be7bf8c29545c02ed87b0067@139.59.169.187:26656,bfc83338eb1448ec42210a5128d1e6cd645fe1fe@134.122.43.87:26656,0a02c82777ee587f0226115408915ccb7e8b32b2@159.203.40.246:26656,dc5e42affe639a14780cf95bc36aba9c0378772e@142.93.150.84:26656,704e223abd5ab11165593a8a1e53bdcbdc8dae69@34.127.20.71:26656,20e9588cbf23d6918d32cfc63bd1f9dc6e6ebebb@134.122.43.96:26656,4ea0406a07d8d9c23c35461367d5f5bd42173c0f@159.203.61.253:26656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.empowerchain/config/config.toml
```

## Servisi Başlatma ve Logları Kontrol Etme
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable empowerd && \
sudo systemctl restart empowerd && \
sudo journalctl -u empowerd -f -o cat
```

**Testnet'in başında bir düğüm çalıştırırsanız ve Genesis'teyseniz, bu mesajı alırsınız**
```bash
Genesis time is in the future. Sleeping until then... genTime=...
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
