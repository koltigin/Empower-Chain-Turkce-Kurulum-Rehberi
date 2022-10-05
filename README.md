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
Node ve Cüzdan adımızın Mehmet olduğunu varsayalım. Kod aşağıdaki şekilde düzenlenecektir. 
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
   'identity'  burayda 'XXXX1111XXXX1111' yazan yere `https://keybase.io` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
   'details'  kendiniz hakkında bilgiler yazabilirsiniz.
   'website'  Varsa bir siteniz yazabilirsiniz olarak bırakabilirsiniz.
   'security-contact'  E-posta adresiniz.
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

Aşağıdaki görselde olduğu gibi gentx-xxxxxxx şeklinde dosyanız oluşacak.
![image](https://user-images.githubusercontent.com/102043225/194017855-c36e8019-922e-4881-9cd7-411ffd39c603.png)


## Gentx Dosyasını Pull Request Etme
Bir önceki komutu girdikten sonra bir gentx dosyası oluşacak. Bu dosya `/.empowerchain/config/gentx/gentx-xxxxxxxxxxxxxxx.json` şeklinde oluşacak. Sunucunuza winscp, termius gibi bir program ile bağlanıp dosyayı bilgisayarınıza kayedebilirsiniz. Daha sonra sırasıyla aşağıdakileri yapıyoruz
 - [EmpowerChain reposunu](https://github.com/empowerchain/empowerchain) forkluyoruz.
 - Forkladığımız repoya kendi profilimizden erişip aşağıda resimde görülen `testnets/altruistic-1/gentx` klasörüne giriyoruz ve `Add file` butonuna basıyoruz.  
 ![Ekran Alıntısı-1](https://user-images.githubusercontent.com/102043225/194023060-4794b8bf-8b84-4d0b-a0a6-63da328aff73.JPG)
 -  Dosyamızın adını aşağıdaki resimde görüldüğü gibi `validator_adiniz-gentx.json` yani şuna benzer `mehmet-gentx.json` şeklinde yazıyoruz.
 -  Yine aşağıdaki resimde görüldüğü gibi indirdiğimiz gentx dosyasının içeriğini metin kutusuna yapıştırıyoruz.
 ![Ekran Alıntısı-2](https://user-images.githubusercontent.com/102043225/194024246-4581b015-d2c4-458d-ba42-7248414db83e.JPG)
 - Daha sonra sayfanın aşağısında bulunan Commit new file butonuna basıyoruz.
 ![Ekran Alıntısı-3](https://user-images.githubusercontent.com/102043225/194024668-4a2417e9-a04a-4887-97ec-451adddc1987.JPG)
 - Ardından açıkan sayfada aşağıda görüldüğü gibi `pull requests` sekmesine tıklıyoruz.
 ![Ekran Alıntısı-4](https://user-images.githubusercontent.com/102043225/194041062-879b8e57-c716-46d6-80c0-ef30e26826f5.JPG)
 - Açılan sayfada bu sefer `New pull request` butonuna basıyoruz.
 ![Ekran Alıntısı-5](https://user-images.githubusercontent.com/102043225/194041394-5150974d-5de8-4cd6-bfa7-6b67549109f3.JPG)
 - Açılan sayfada bu sefer `Create pull request` butonuna basıyoruz.
 ![Ekran Alıntısı-6](https://user-images.githubusercontent.com/102043225/194041795-48c65277-7cd0-485f-bc7e-aa4ea4681631.JPG)
 - Açılan ekranda da aşağıdaki resimdeki gibi `Create pull request` butonuna basıyoruz ve işlemi tamamlıyoruz.
 ![Ekran Alıntısı-7](https://user-images.githubusercontent.com/102043225/194042453-4ae3b033-033a-401d-87cf-eb451830f183.jpg)


🟡 **config** dosyası içerisinde ki **priv_validator_key.json** dosyasını yedek almayı unutmayınız!

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
Çıktı şu şekilde olmalıdır
  `Result: fcae4a283488be14181fdc55f46705d9e11a32f8e3e8e25da5374914915d5ca8`

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
peers="9de92b545638f6baaa7d6d5109a1f7148f093db3@65.108.77.106:26656,4fd5e497563b2e09cfe6f857fb35bdae76c12582@65.108.206.56:26656,fe32c17373fbaa36d9fd86bc1146bfa125bb4f58@5.9.147.185:26656,220fb60b083bc4d443ce2a7a5363f4813dd4aef4@116.202.236.115:26656,225ad85c594d03942a026b90f4dab43f90230ea0@88.99.3.158:26656,2a2932e780a681ddf980594f7eacf5a33081edaf@192.168.147.43:26656,333de3fc2eba7eead24e0c5f53d665662b2ba001@10.132.0.11:26656,4a38efbae54fd1357329bd583186a68ccd6d85f9@94.130.212.252:26656,52450b21f346a4cf76334374c9d8012b2867b842@167.172.246.201:26656,56d05d4ae0e1440ad7c68e52cc841c424d59badd@192.168.1.46:26656,6a675d4f66bfe049321c3861bcfd19bd09fefbde@195.3.223.204:26656,1069820cdd9f5332503166b60dc686703b2dccc5@138.201.141.76:26656,277ff448eec6ec7fa665f68bdb1c9cb1a52ff597@159.69.110.238:26656,3335c9458105cf65546db0fb51b66f751eeb4906@5.189.129.30:26656,bfb56f4cb8361c49a2ac107251f92c0ea5a1c251@192.168.1.177:26656,edc9aa0bbf1fcd7433fcc3650e3f50ab0becc0b5@65.21.170.3:26656,d582bcd8a8f0a20c551098571727726bc75bae74@213.239.217.52:26656,eb182533a12d75fbae1ec32ef1f8fc6b6dd06601@65.109.28.219:26656,b22f0708c6f393bf79acc0a6ca23643fe7d58391@65.21.91.50:26656,e8f6d75ab37bf4f08c018f306416df1e138fd21c@95.217.135.41:26656,ed83872f2781b2bdb282fc2fd790527bcb6ffe9f@192.168.3.17:26656"
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

🔴 **Faucet açıldıktan sonra validator oluşturacağız.**


## Validator Oluşturma
 Aşağıdaki komutta aşağıda berlittiğim yerler dışında bir değişikli yapmanız gerekmez;
   'identity'  burada `XXXX1111XXXX1111` yazan yere `httpskeybase.io` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
   'details'  `Always forward with the Anatolian Team 🚀` yazan yere kendiniz hakkında bilgiler yazabilirsiniz.
   'website'  `https://anatolianteam.com` yazan yere varsa bir siteniz ya da twitter vb. adresinizi yazabilirsiniz.
   'security-contact'  E-posta adresiniz.
```shell 
strided tx staking create-validator \
--amount=9900000umpwr \
--pubkey=$(strided tendermint show-validator) \
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


# EmpowerChain

This is where the code for anything related to EmpowerChain is located. Documentation is being built right now.

In the meantime, take a look at our [whitepaper](https://github.com/empowerchain/empowerchain/blob/main/Whitepaper.pdf), [website](https://empowerchain.io) and [@empowerchain_io on twitter](https://twitter.com/empowerchain_io).

# ./chain

In the chain folder is where the actual blockchain-code is located.

# ./docs

In the docs folder you will find the documentation and doc site for https://docs.empowerchain.io

