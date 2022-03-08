# CosmicHorizon



TASK 2 - Custom IBC


1)INSTALL GO

ver="1.17"

cd $HOME

wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"

sudo rm -rf /usr/local/go

sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"

rm "go$ver.linux-amd64.tar.gz"

echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile

source $HOME/.bash_profile

go version
#go version go1.17 linux/amd64



2) Installing gaiad

git clone https://github.com/cosmos/gaia

cd gaia

git checkout v6.0.3

make build

cd build

rm -rf gaiad

wget https://transfer.sh/get/NEsjMq/gaiad

cd..

sudo cp  $HOME/gaia/build/gaiad /usr/local/bin/gaiad

chmod +x  /usr/local/bin/gaiad

gaiad version

--------ÇIKTI BU ŞEKILDE OLACAK------
-----main-066ce7a077360b2e4945b10f60567008009d92a8------

cd

gaiad keys add wallet --keyring-backend=test

-------KEY CIKTILARINI KAYDET-------

aşağıda maniker bilginizi girin.

MONIKER=Ugurk

CUSTOM_CHAINID=${MONIKER,,}-1

gaiad init ${MONIKER} --chain-id $CUSTOM_CHAINID

-------------çıktıyı kaydet------

gaiad add-genesis-account $(gaiad keys show wallet -a --keyring-backend=test) 1000000000stake,1000000000${MONIKER,,}

gaiad gentx wallet 1000000000stake --chain-id $CUSTOM_CHAINID --keyring-backend=test --moniker=$MONIKER

-----genesis oluştu----

gaiad collect-gentxs --gentx-dir ~/.gaia/config/gentx/

---ÇIKTIYI KAYDET---

echo "export MONIKER=${MONIKER}" >> $HOME/.bash_profile

echo "export CUSTOM_CHAINID=${CUSTOM_CHAINID}" >> $HOME/.bash_profile

source $HOME/.bash_profile

aşağıda 26 ile başlayan portları 36yap

nano .gaia/config/config.toml

proxy_app = "tcp://127.0.0.1:36658"

laddr = "tcp://127.0.0.1:36657"

laddr = "tcp://0.0.0.0:36656"

prometheus_listen_addr = ":36660"


90 ile başlayan portları 91 yap

nano .gaia/config/config.toml

address = "0.0.0.0:9190"

address = "0.0.0.0:9191"

------------------------------


cd .hermes

cp config.toml eskiconfig.toml

nano config.toml

burda [[chains]] altında devnet-2 kodlarını sil

ve aşağıdakini ekle.

[[chains]]
id = 'monikeradin-1'
rpc_addr = 'http://localhost:36657'
grpc_addr = 'http://localhost:9190'
websocket_addr = 'ws://localhost:36657/websocket'
rpc_timeout = '10s'
account_prefix = 'cosmos'
key_name = 'wallet'
store_prefix = 'ibc'
max_tx_size = 100000
max_gas = 20000000
gas_price = { price = 0.001, denom = '--------MONIKERADI-------' }
gas_adjustment = 0.5
max_msg_num = 15
clock_drift = '5s'
trusting_period = '9days'
trust_threshold = { numerator = '1', denominator = '3' }

---------------------------------------------------------------
hermes içerisine gaia walleti import edecez.

hermes keys restore MONIKERADI-1 -n wallet -m "MNEMONIC KEY"

------------------------------------------------
sudo tee /etc/systemd/system/gaiad.service > /dev/null <<EOF
Description=GAIAD
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which gaiad) start --rpc.laddr=tcp://0.0.0.0:36657 --grpc.address="0.0.0.0:9190"
Restart=on-failure
RestartSec=10
LimitNOFILE=4096
[Install]
WantedBy=multi-user.target
EOF


sudo systemctl daemon-reload && sudo systemctl enable gaiad
                                                             
sudo systemctl restart gaiad && journalctl -u gaiad -f
--------------------------------------------------------------------
gaiad q bank balances cosmos1............. --node http://127.0.0.1:36657/

balances:
- amount: "1000000000"
  denom: ugurk
pagination:
  next_key: null
  total: "0"




--------------------------------------------------
hermes create channel lcofjurn-1 darkmatter-1 --port-a transfer --port-b transfer

------------ÇIKITIYI KAYDET----------

SUCCESS yazısını gördükten sonra altta a ve b side içindeki channel id leri not al.


------TX görevleri---------

------yukarıdaki görevden gelen channel-0 olmayan idyi aşağıdaki kodda kullanacaksın.-------

hermes tx raw ft-transfer MONIKERADIN-1 darkmatter-1 transfer channel-182 100 --denom ucoho -n 1 --timeout-seconds 699

SUCCESS yazısı üzerindeki tx hash i kaydet. bu görevin json karşılığı "src2dstTxHash": olacak. 
TX bu siteden sorgulayabilirsin.
http://explorer.d-stake.xyz/darkmatter-1


hermes tx raw ft-transfer  darkmatter-1 MONIKERADIN-1 transfer channel-0 10 --denom MONIKERADIN -n 1 --timeout-seconds 699
SUCCESS yazısı üzerindeki tx hash i kaydet. bu görevin json karşılığı "dst2srcTxHash": olacak.

bu tx sorgulayamıyorsun. Görevde yapıştır gönder.


