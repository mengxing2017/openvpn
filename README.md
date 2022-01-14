## 参考文献
[openvpn for Docker](https://github.com/kylemanna/docker-openvpn)

## 部署
1. 配置部署环境变量
```shell
export OVPN_DATA="ovpn-data-example"
export IP="VPN.EXAMPLE.COM"
```
2. 初始化容器配置文件和证书
```shell
docker volume create --name $OVPN_DATA
docker run -v $OVPN_DATA:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://$IP
docker run -v $OVPN_DATA:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki
```
3. 启动`openVPN`服务端
```shell
docker run --name openvpn --restart=always -v $OVPN_DATA:/etc/openvpn -d -p 1194:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn
```
4. 生成客户端证书和密钥
```shell
docker run -v $OVPN_DATA:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full CLIENTNAME nopass
```
5. 导出客户端证书
```shell
docker run -v $OVPN_DATA:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient CLIENTNAME > CLIENTNAME.ovpn
```
6. 客户端连接
```shell
openvpn --config CLIENTNAME.ovpn
```
