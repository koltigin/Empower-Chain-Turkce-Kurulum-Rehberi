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
--moniker=$EMPOW_NODENAME \
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
