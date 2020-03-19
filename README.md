1. Перед началом работы с Fabric, необходимо установить и настроить PostgreSQL(Создать БД fabric_ca). 
2. Создаем папку `sudo mkdir /var/hyperledger` (тут будут храниться данные пиров) и `sudo chmod 777 /var/hyperledger`
3. Скачиваем бинарники https://drive.google.com/drive/folders/1JaI61luousp_kvY53hxb-OCf5HAmdaE1?usp=sharing
	`cd ./CA` - переходим в папку `CA`
3. `export PATH=<path to download location>/bin:$PATH`    - (добавляем бинарники в глобальную переменную PATH) (`export PATH=$PWD/bin:$PATH`)
4. `fabric-ca-server start -b admin:adminpwd`      -   (запускаем CA тем самым создаем конфигурационный файл с параметрами по умолчанию)
5. в конфигурационном файле (fabric-ca-server-config.yaml) изменяем настройки базы данных + меняем affiliations + меняем настройки csr
6. `fabric-ca-server start -b admin:adminpwd`     - снова запускаем CA
7. `fabric-ca-client enroll --url http://admin:adminpwd@localhost:7054`  - авторизуемся админом
8. `fabric-ca-client register --id.name peer  --id.secret peerpwd  --id.type peer  --id.affiliation org1.department1`   - регистрация пира в СА
9. `fabric-ca-client enroll -u http://peer:peerpwd@localhost:7054` - авторизируемся peer'ом
10. `cd ./Peer` - переходим в папку `Peer`
11. Копируем `~/.fabric-ca-client/msp` в `./data/msp` 
12. Создаём папку `./data/msp/admincerts`.
13. Копируем сертификат администратора в `./data/msp/admincerts`
14. Копируем `config.yaml` в `./data/msp/`
15. Меняем значение полей `./data/msp/config.yaml` на актуальное название сертификата CA `./data/msp/cacerts` (`localhost-7054.pem`) 
16. В `./data/core.yaml` меняем следующие натсройки:
    - `peer.address` - актуальный адресс пира
    - `peer.id` - на актуальное имя пира
    - `peer.localMspId` - на актуальную организацию пира
    - `operations.listenAddress` на `127.0.0.1:9444`
    - `peer.chaincodeListenAddress` на `127.0.0.1:7052`
    - `peer.fileSystemPath` на `/var/hyperledger/production/peers/peer1` (место хранения данных peer1) 
17. `cd data`  и запустить `peer node start`
18. `fabric-ca-client enroll --url http://admin:adminpwd@localhost:7054`  - авторизуемся админом
19. `fabric-ca-client register --id.name peer2  --id.secret peerpwd  --id.type peer  --id.affiliation org1.department2`   - регистрация второго пира в СА
20. `fabric-ca-client enroll -u http://peer2:peerpwd@localhost:7054` - авторизируемся peer'ом
21. `cd ./Peer2` - переходим в папку `Peer2`
22. Копируем `~/.fabric-ca-client/msp` в `./data/msp` 
23. Создаём папку `./data/msp/admincerts`.
24. Копируем сертификат администратора в `./data/msp/admincerts`
25. Копируем `config.yaml` в `./data/msp/`
26. Меняем значение полей `./data/msp/config.yaml` на актуальное название сертификата CA `./data/msp/cacerts` (`localhost-7054.pem`) 
27. В `./data/core.yaml` меняем следующие настройки:
    - `peer.address` - актуальный адресс пира
    - `peer.id` - на актуальное имя пира
    - `peer.localMspId` - на актуальную организацию пира
    - `operations.listenAddress` на `127.0.0.1:9445`
    - `peer.chaincodeListenAddress` на `127.0.0.1:7056`
    - `peer.fileSystemPath` на `/var/hyperledger/production/peers/peer2` (место хранения данных peer2) 
28. `cd data`  и запустить `peer node start`
29. `fabric-ca-client enroll --url http://admin:adminpwd@localhost:7054`  - авторизуемся админом
30. `fabric-ca-client register --id.name orderer  --id.secret orderpwd --id.type peer --id.affiliation org1.department1`
31. `fabric-ca-client enroll -u http://orderer:orderpwd@localhost:7054`
32. `cd ./Order` - переходим в папку `Order`
33. Копируем `~/.fabric-ca-client/msp` в `./data/msp` 
34. Создаём папку `./data/msp/admincerts`.
35. Копируем сертификат администратора в `./data/msp/admincerts`
36. Копируем `config.yaml` в `./data/msp/`
37. Меняем значение полей `./data/msp/config.yaml` на актуальное название сертификата CA `./data/msp/cacerts` (`localhost-7054.pem`) 
38. `cryptogen generate --config crypto-config.yaml`
39. `cd data` и редактируем `configtx.yaml`
    - Смена название организации на актуальную по всему конфигу
    - `Orderer.EtcdRaft.Consenters.Port на 7070`
    - В `Orderer.EtcdRaft.Consenters` `ClientTLSCert` и `ServerTLSCert` путь на TLS сертификат сервера, который создан командой `cryptogen` (`server.crt`)
40. В файле `orderer.yaml` отредактировать:
    - General.Cluster:
        * `ListenPort: 7070`
        * `ListenAddress: 127.0.0.1`
        * `ServerCertificate:` - путь на TLS сертификат сервера, который создан командой `cryptogen` (`server.crt`)
        * `ServerPrivateKey:` - путь на приватный ключ TLS сервера, который создан командой `cryptogen` (`server.key`)
    - `General.GenesisProfile: SampleDevModeEtcdRaft` -  `SampleDevModeEtcdRaft` для запуска c алгоритмом `Raft`
41. `cd data`  и запустить `orderer`


