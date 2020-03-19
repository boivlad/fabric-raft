1. Перед началом работы с Fabric, необходимо установить и настроить PostgreSQL(Создать БД fabric_ca). 
2. git clone https://github.com/boivlad/fabric-raft.git
3. Создаем папку `sudo mkdir /var/hyperledger` (тут будут храниться данные пиров) и `sudo chmod 777 /var/hyperledger`
4. Скачиваем бинарники в директорию `fabric-raft` https://drive.google.com/drive/folders/1JaI61luousp_kvY53hxb-OCf5HAmdaE1?usp=sharing
5. `cd ./CA` - переходим в папку `CA`
6. `export PATH=<path to download location>/bin:$PATH`    - (добавляем бинарники в глобальную переменную PATH) (`export PATH=$PWD/bin:$PATH`)
7. `fabric-ca-server start -b admin:adminpwd`      -   (запускаем CA тем самым создаем конфигурационный файл с параметрами по умолчанию)
8. в конфигурационном файле (fabric-ca-server-config.yaml) изменяем настройки базы данных + меняем affiliations + меняем настройки csr
9. `fabric-ca-server start -b admin:adminpwd`     - снова запускаем CA
10. `fabric-ca-client enroll --url http://admin:adminpwd@localhost:7054`  - авторизуемся админом
11. `fabric-ca-client register --id.name peer  --id.secret peerpwd  --id.type peer  --id.affiliation org1.department1`   - регистрация пира в СА
12. `fabric-ca-client enroll -u http://peer:peerpwd@localhost:7054` - авторизируемся peer'ом
13. `cd ./Peer` - переходим в папку `Peer`
14. Копируем `~/.fabric-ca-client/msp` в `./data/msp` 
15. Создаём папку `./data/msp/admincerts`.
16. Копируем сертификат администратора в `./data/msp/admincerts`
17. Копируем `config.yaml` в `./data/msp/`
18. Меняем значение полей `./data/msp/config.yaml` на актуальное название сертификата CA `./data/msp/cacerts` (`localhost-7054.pem`) 
19. В `./data/core.yaml` меняем следующие натсройки:
    - `peer.address` - актуальный адресс пира
    - `peer.id` - на актуальное имя пира
    - `peer.localMspId` - на актуальную организацию пира
    - `operations.listenAddress` на `127.0.0.1:9444`
    - `peer.chaincodeListenAddress` на `127.0.0.1:7052`
    - `peer.fileSystemPath` на `/var/hyperledger/production/peers/peer1` (место хранения данных peer1) 
20. `cd data`  и запустить `peer node start`
21. `fabric-ca-client enroll --url http://admin:adminpwd@localhost:7054`  - авторизуемся админом
22. `fabric-ca-client register --id.name peer2  --id.secret peerpwd  --id.type peer  --id.affiliation org1.department2`   - регистрация второго пира в СА
23. `fabric-ca-client enroll -u http://peer2:peerpwd@localhost:7054` - авторизируемся peer'ом
24. `cd ./Peer2` - переходим в папку `Peer2`
25. Копируем `~/.fabric-ca-client/msp` в `./data/msp` 
26. Создаём папку `./data/msp/admincerts`.
27. Копируем сертификат администратора в `./data/msp/admincerts`
28. Копируем `config.yaml` в `./data/msp/`
29. Меняем значение полей `./data/msp/config.yaml` на актуальное название сертификата CA `./data/msp/cacerts` (`localhost-7054.pem`) 
30. В `./data/core.yaml` меняем следующие настройки:
    - `peer.address` - актуальный адресс пира
    - `peer.id` - на актуальное имя пира
    - `peer.localMspId` - на актуальную организацию пира
    - `operations.listenAddress` на `127.0.0.1:9445`
    - `peer.chaincodeListenAddress` на `127.0.0.1:7056`
    - `peer.fileSystemPath` на `/var/hyperledger/production/peers/peer2` (место хранения данных peer2) 
31. `cd data`  и запустить `peer node start`
32. `fabric-ca-client enroll --url http://admin:adminpwd@localhost:7054`  - авторизуемся админом
33. `fabric-ca-client register --id.name orderer  --id.secret orderpwd --id.type peer --id.affiliation org1.department1`
34. `fabric-ca-client enroll -u http://orderer:orderpwd@localhost:7054`
35. `cd ./Order` - переходим в папку `Order`
36. Копируем `~/.fabric-ca-client/msp` в `./data/msp` 
37. Создаём папку `./data/msp/admincerts`.
38. Копируем сертификат администратора в `./data/msp/admincerts`
39. Копируем `config.yaml` в `./data/msp/`
40. Меняем значение полей `./data/msp/config.yaml` на актуальное название сертификата CA `./data/msp/cacerts` (`localhost-7054.pem`) 
41. `cryptogen generate --config crypto-config.yaml`
42. `cd data` и редактируем `configtx.yaml`
    - Смена название организации на актуальную по всему конфигу
    - `Orderer.EtcdRaft.Consenters.Port на 7070`
    - В `Orderer.EtcdRaft.Consenters` `ClientTLSCert` и `ServerTLSCert` путь на TLS сертификат сервера, который создан командой `cryptogen` (`server.crt`)
43. В файле `orderer.yaml` отредактировать:
    - General.Cluster:
        * `ListenPort: 7070`
        * `ListenAddress: 127.0.0.1`
        * `ServerCertificate:` - путь на TLS сертификат сервера, который создан командой `cryptogen` (`server.crt`)
        * `ServerPrivateKey:` - путь на приватный ключ TLS сервера, который создан командой `cryptogen` (`server.key`)
    - `General.GenesisProfile: SampleDevModeEtcdRaft` -  `SampleDevModeEtcdRaft` для запуска c алгоритмом `Raft`
44. `cd data`  и запустить `orderer`


