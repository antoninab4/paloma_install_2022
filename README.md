### Установка ноды Paloma Пошаговое руководство.

### дискорд проекта 
https://discord.gg/mFxgPfgQRg

### сайт проекта
https://palomachain.com/

## Характеристики сервера для ноды

# 4/8/200


Протокол Paloma — это самый быстрый, безопасный и масштабируемый
оракул для обмена сообщениями между сетями и управления газами. Paloma — это новый протокол блокчейна для DeFi, NFT и сетевых игр.

Уникальное ценностное предложение Paloma — это ее высокоскоростная архитектура ретрансляции сообщений, разработанная для минимизации задержки связи между конечными точками блокчейна. Валидаторы Paloma заинтересованы в обеспечении максимальной скорости ретрансляции, выполнения вычислений и времени безотказной работы.

Мотивация
Экономическая модель Paloma проста: валидаторы получают компенсацию газа за ретрансляцию сообщений между цепочками от имени отправителей сообщений, которые хотят как можно быстрее
отправлять сообщения в несколько цепочек .

Плата за газ, безопасность и скорость оплачивается либо в валюте Paloma GRAIN, либо в национальной валюте целевой сети, получающей сообщение. Палома — это рынок оплаты за доставку сообщений.

Валидаторы соревнуются за обеспечение высокой пропускной способности сообщений в максимально возможном количестве цепочек с наименьшими затратами и минимально возможной задержкой ответа. Стейкеры токенов Paloma получают часть дохода от платы за ретрансляцию сообщений в качестве вознаграждения за стейкинг.

## Подготовим сервер для работы, установим все пакеты.

```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install make clang pkg-config libssl-dev build-essential git gcc chrony curl jq ncdu bsdmainutils htop net-tools lsof fail2ban wget -y
```

## Далее установим основную часть со снепом сразу с помощью скрипта ниже (благодарим nodejumper)
```
source <(curl -s https://raw.githubusercontent.com/nodejumper-org/cosmos-scripts/master/paloma/paloma-testnet-13/install.sh)
```

## После установки нам только останется дождаться пока нода догонит высоту по текущим блокам, посмотреть синхрон можно командой ниже. (false значит все)
```
palomad status 2>&1 | jq .SyncInfo.catching_up
```
## Теперь пока нода синхронизируется выполняем следующие ВАЖНЫЕ действия, ОЧЕНЬ ВНИМАТЕЛЬНО !!! Так как у Вас там будут реал средства для комиссий, которые разработчики компенсируют, тоесть без наших затрат,просто держим на комиссию.

## Создать ключи ETH/BNB/PALOMA (ВСЕ ВНИМАТЕЛЬНО)

### 1. Создаем ключ для ETH
```
pigeon evm keys generate-new $HOME/.pigeon/keys/evm/eth-main
```
### 2. Создаем ключ для BNB 
```
pigeon evm keys generate-new $HOME/.pigeon/keys/evm/bnb-main
```
### 3. Создаем кошелек для Paloma (тестовый) сохраняем СИД ФРАЗУ и данныекошелька.
```
palomad keys add wallet
```

## Задаем пароли для каждого кошелька (копируем в блокнот, вносим изменения и выполняем построчно) Скобки <> УБИРАЕМ !!!!
```
ETH_PASSWORD=<YOUR_ETH_PASSWORD>
BNB_PASSWORD=<YOUR_ETH_PASSWORD>
PALOMA_KEYRING_PASS=<YOUR_PALOMA_PASSWORD>
```
## Прописываем адреса кошельков для созданных ключей (Понадобятся для пополнения реальными деньгами )
```
ETH_SIGNING_KEY=0x$(cat $HOME/.pigeon/keys/evm/eth-main/*  | jq -r .address | head -n 1)
BSC_SIGNING_KEY=0x$(cat $HOME/.pigeon/keys/evm/bnb-main/*  | jq -r .address | head -n 1)
```
## команда будет пополнять баланс автоматически, каждый день, чтобы вы не потеряли свои средства на оба кошелька

## Создаем сервисный файл (копируем все целиком и выполняем)
```
sudo tee $HOME/.pigeon/env.sh > /dev/null << EOF
PALOMA_KEYRING_PASS=$PALOMA_KEYRING_PASS
ETH_RPC_URL=https://eth-mainnet.g.alchemy.com/v2/9bYS5h99MVmaa7f0fYztaHwN31k2EBvZ
ETH_PASSWORD=$ETH_PASSWORD
ETH_SIGNING_KEY=$ETH_SIGNING_KEY
BNB_RPC_URL=https://wispy-falling-tent.bsc.discover.quiknode.pro/750bbdfab9cd076e37a35b91513b47e59ad8fc51
BNB_PASSWORD=$BNB_PASSWORD
BNB_SIGNING_KEY=$BSC_SIGNING_KEY
WALLET=wallet
EOF
```
## Рестартим его 
```
sudo systemctl restart pigeond
```
## Идем за тестовыми токенами или в дискорд в кран или на сайт , вводим адрес от кошелька Paloma и запрашиваем токены
```
https://faucet.palomaswap.com
```
### Ожидаем полной синхронизации, что бы увидеть их на балансе.
```
palomad status 2>&1 | jq .SyncInfo.catching_up
```
## Проверяем баланс на кошельке Paloma
```
palomad q bank balances $(palomad keys show wallet -a)
```
#### Вывод будет примерно такой 
````
## Console output
#  balances:
#  - amount: "10000000"
#    denom: ugrain
````

## Пополняем наши созданные ETH и BNB токенами 0,1 ETH и 0.1 BNB (каждый в своей сети). Восстанавливаем 2 кошелька Метамаск по ключам с ноды для удобства.
```
https://metamask.zendesk.com/hc/en-us/articles/360015489331-How-to-import-an-account#h_01G01W0D3TGE72A7ZBV0FMSZX1
```
#### Проверяем адреса в ММ и в ноде для сетей эфира и бнб что бы совпадали.

## Все готово для создания валидатора. Сначала копируем в блокнот все целиком , редактируем имя валидатора , затем вставляем все целиком !!!!!
```
palomad tx staking create-validator \
--amount=9000000ugrain \
--pubkey=$(palomad tendermint show-validator) \
--moniker="здесь_имя_вашего_моникера" \
--chain-id=paloma-testnet-13 \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--fees=2000ugrain \
--from=wallet \
-y
```
## После создания записываем данные валидатора, вывод командой 
```
palomad q staking validator $(palomad keys show wallet --bech val -a)
```

## Переходим в эксплоер и смотрим что бы у нас нода отобоажалась в валидаторах и подписывала блоки.
```
https://paloma.explorers.guru/validators
```
