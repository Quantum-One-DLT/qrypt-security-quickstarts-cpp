# Basic Quickstart Setup
This tutorial demonstrates the steps to setup basic Qrypt Security SDK. It clones this quickstart repo, builds the sample apps codes to generate a command line tool for distributed key gen. Then it runs the command line tool to generate a key (as Alice) and recover the key (as Bob) respectively.

## Setup environment

The commands shown in this tutorial should be run on an Ubuntu 20.04 system.

## Prerequisites
- A Qrypt Account. [Create an account for free](https://portal.qrypt.com/register)

## Setup
1. *Optional: If you have docker installed on the system (e.g. Mac OS), you could run Alice and Bob in Ubuntu containers instead of Ubuntu desktops.*
    ```
    docker run --name qrypt_ubuntu -it --rm ubuntu:20.04 bash
    ```

1. Retrieve a token from the [Qrypt Portal](https://portal.qrypt.com/tokens).
    
    Create an environment variable **QRYPT_TOKEN** for the token.
    ```
    export QRYPT_TOKEN="eyJhbGciOiJ......"
    ```
    *Optional: to set QRYPT_TOKEN permanently for all future bash sessions, put it in ~/.bashrc*
    ```
    export QRYPT_TOKEN="eyJhbGciOiJ......" >> ~/.bashrc
    ```
1. Install the development and network tools.
    ```
    apt-get update
    DEBIAN_FRONTEND="noninteractive" TZ="America/New_York" apt-get install -y cmake git gcc g++ libgtest-dev curl jq
    ```

1. Clone the [repo](https://github.com/QryptInc/qrypt-security-quickstarts-cpp) containing this quickstart to a local folder.
    ```
    git clone https://github.com/QryptInc/qrypt-security-quickstarts-cpp.git
    cd qrypt-security-quickstarts-cpp
    ```

1. Download the Qrypt Security SDK from the [Qrypt Portal](https://portal.qrypt.com/downloads/sdk-downloads) for Ubuntu.

1. Extract the Qrypt SDK into the /qrypt-security-quickstarts-cpp/KeyGenDistributed/lib/QryptSecurity folder
    ```
    tar -zxvf <sdk_file> --strip-components=1 -C KeyGenDistributed/lib/QryptSecurity
    ```
    *Optional: At this point you should be able to see the header files and libraries under KeyGenDistributed/lib/QryptSecurity.*
    ```
    # Expected output:  include  lib  licenses
    ls KeyGenDistributed/lib/QryptSecurity/ 
    ```

1. Build the keygen tool.
    ```
    cd KeyGenDistributed
    ./build.sh
    ```
    
    *Optional: to make a debug build*
    ```
    ./build.sh --build_type=Debug
    ```

## Test commands
#### Test OTP keygen
    
Alice generates the OTP key and metadata file.
  
```
build/KeyGenDistributed --user=alice --token=$QRYPT_TOKEN --key-type=otp --otp-len=32768 --metadata-filename=otp_metadata.bin --key-filename=alice_otp.bin
```
    
Bob recovers the OTP key using the metadata file. This key should be identical to Alice's OTP key.
```
build/KeyGenDistributed --user=bob --token=$QRYPT_TOKEN --metadata-filename=otp_metadata.bin --key-filename=bob_otp.bin
```


#### Test AES keygen
Alice generates the AES key and metadata file.

```
build/KeyGenDistributed --user=alice --token=$QRYPT_TOKEN --key-type=aes --metadata-filename=aes_metadata.bin --key-filename=alice_aes.bin
```

Bob recovers the AES key using the metadata file. This key should be identical to Alice's AES key.
```
build/KeyGenDistributed --user=bob --token=$QRYPT_TOKEN --metadata-filename=aes_metadata.bin --key-filename=bob_aes.bin
```
