# Clone this repository
```bash
git clone https://github.com/fbsobreira/mcash-ledger.git
cd mcash-ledger
```


# Compiling from source

## Docker toolchain image
In order to make compiling as eas as possible you can make use of a docker image containing all the necessary compilers and the [nanos-secure-sdk](https://github.com/LedgerHQ/nanos-secure-sdk).

Make sure you have [Docker](https://www.docker.com/community-edition) installed.

### Step 1 - Build the image:
> make sure to select the appropriate $BOLOS_SDK in Dockerfile
```bash
docker build -t ledger-chain:latest .
```
The `.` at the end is **important!**

 
### Step 2 - Use Docker image
```bash
docker run --rm -v "$(pwd)":/mcash-ledger -w /mcash-ledger ledger-chain make
```

## Using your own toolchain
```bash
make
```


# Load app onto Ledger Nano S

Before attempting to load the hex file, make sure your Ledger Nano S 
is connected and the firware is updated to the [latest version](https://support.ledgerwallet.com/hc/en-us/articles/360002731113-Update-the-firmware).

Enter your PIN and **make sure you're seeing the Dashboard app**.

## Using Docker image
### Step 1 - Install virtualenv
```bash
[sudo] pip install -U setuptools
[sudo] pip install virtualenv
```

### Step 2 - Create new virtualenv
#### linux dependencies for ledgerblue module
> libudev1 libudev-dev libusb-1.0-0-dev

```bash
virtualenv -p python3 ledger
source ledger/bin/activate
pip install ledgerblue
or pip install git+https://github.com/LedgerHQ/blue-loader-python.git 
```

If you run into errors here, make sure you have the required dependencies installed. See [Ledger - Loader Python](https://github.com/LedgerHQ/blue-loader-python).

### Step 3 - Load HEX file
```bash
python -m ledgerblue.loadApp \
--targetId 0x31100004 \
--fileName bin/app.hex \
--icon `docker run --rm -v "$(pwd)":/mcash_ledger -w /mcash_ledger ledger-chain sh -c 'python $BOLOS_SDK/icon.py nanos_app_mcash.gif hexbitmaponly'` \
--curve secp256k1 \
--path "44'/60'" \
--apdu \
--appName "MCashChain" \
--appVersion `cat ./VERSION` \
--appFlags 0x40 \
--dataSize $((0x`cat debug/app.map |grep _envram_data | tr -s ' ' | cut -f2 -d' '|cut -f2 -d'x'` - 0x`cat debug/app.map |grep _nvram_data | tr -s ' ' | cut -f2 -d' '|cut -f2 -d'x'`)) \
--delete \
--tlv 
```

### Step 4 - Leave virtualenv
To get out of your Python virtualenv again after everything is done.
```bash
deactivate
```

## Using your own toolchain

```bash
make load
```

### Step 1 - Install virtualenv
See step 1 above. 

### Step 2 - Create new virtualenv
See step 2 above. 

### Step 3 - Load HEX file
```bash
python -m ledgerblue.loadApp \
--targetId 0x31100004 \
--fileName release/app.hex.0.1.1 \
--icon 0100000000ffffff00ffffffffffffffff3ff0bff70ff4aff52ff0effd0ffcffffffffffffffffffff \
--curve secp256k1 \
--path "44'/60'" \
--apdu \
--appName "MCashChain" \
--appVersion "0.1.1" \
--appFlags 0x40 \
--delete \
--dataSize 64 \
--tlv 
```
Replace `NAME_OF_PRECOMPILED_HEX_HERE.hex` with the location and name of the pre-compiled HEX file.

Replace `VERSION_NUMBER` with the version number of the pre-compiled HEX file.

Replace `DATA_SIZE_OF_PRECOMPILED_HEX` with the data size of the pre-compiled HEX file.

### Step 4 - Leave virtualenv
See step 4 above.

# Links
========

### [Mcashscan Integration](https://mcashscan.io)
