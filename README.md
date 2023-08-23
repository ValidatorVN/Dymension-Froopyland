# Dymension-Froopyland

    VPS của Digital Ocean, chi phí 5$ tặng 200$ cho người mới tham gia lần đầu: https://bit.ly/DigitalOcean200 
    Video hướng dẫn: https://youtu.be/IdofxspCOQk

Cấu hình khuyến nghị:

    4 core CPU
    8GB Ram
    200GB NVMe

# Bắt đầu cài đặt Node

1/ Update VPS:

    apt update && apt upgrade -y

2/ Cài công cụ cần thiết:

    sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential git make ncdu -y

Cài Golang >= 1.18.x

    ver="1.19.1" 
    cd $HOME 
    wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" 


    sudo rm -rf /usr/local/go 
    sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" 
    rm "go$ver.linux-amd64.tar.gz"

    echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
    source $HOME/.bash_profile

3/ Tải về bộ cài node:

    git clone https://github.com/dymensionxyz/dymension.git --branch v1.0.2-beta
    cd dymension
    make install

    dymd init node --chain-id=froopyland_100-1

4/ Tạo ví, lưu lại 24 kí tự đặc biệt:

    dymd keys add wallet

5/ Tải về 3 file, chép vào đường dẫn:

    /root/.dymension/config/

 6/ Cài SystemD:

    sudo tee /etc/systemd/system/dymd.service > /dev/null << EOF
    [Unit]
    Description=Dymension testnet Node
    After=network-online.target
    [Service]
    User=$USER
    ExecStart=$(which dymd) start
    Restart=on-failure
    RestartSec=10
    LimitNOFILE=10000
    [Install]
    WantedBy=multi-user.target
    EOF

    dymd tendermint unsafe-reset-all --home $HOME/.dymension --keep-addr-book

Check logs:

    sudo systemctl daemon-reload
    sudo systemctl enable dymd
    sudo systemctl restart dymd

    sudo journalctl -u dymd -f --no-hostname -o cat

7/ Tạo validator:

    dymd tx staking create-validator \
    --amount=1000000udym \
    --pubkey=$(dymd tendermint show-validator) \
    --moniker=Node \
    --details="https://linktr.ee/validatorvn" \
    --chain-id=froopyland_100-1 \
    --commission-rate=0.10 \
    --commission-max-rate=0.20 \
    --commission-max-change-rate=0.01 \
    --min-self-delegation=1 \
    --from=wallet \
    --gas-prices=0.1udym \
    --gas-adjustment=1.5 \
    --gas=auto \
    -y

Cộng đồng chạy Node Việt Nam: 

    https://t.me/NodeValidatorVN
