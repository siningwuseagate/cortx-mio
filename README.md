[![Codacy Badge](https://app.codacy.com/project/badge/Grade/a3d60ecc5d8942c9a4b04bcf4b60bf20)](https://www.codacy.com/gh/Seagate/cortx/dashboard?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=Seagate/cortx&amp;utm_campaign=Badge_Grade)
[![license](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/Seagate/cortx/blob/main/LICENSE)
[![Slack](https://img.shields.io/badge/chat-on%20Slack-blue")](https://cortx.link/join-slack)
[![YouTube](https://img.shields.io/badge/Video-YouTube-red)](https://cortx.link/videos)
[![Latest Release](https://img.shields.io/github/v/release/Seagate/cortx?label=Latest%20Release)](https://github.com/seagate/cortx/releases/latest)
[![GitHub contributors](https://img.shields.io/github/contributors/Seagate/cortx)](https://github.com/Seagate/cortx/graphs/contributors/)
[![SODA Eco project](https://img.shields.io/badge/SODA-ECO%20Project-9cf)](./doc/Soda-welcome-page.md)

## README
Copyright: (c) 2020 - 2021 Seagate Technology LLC and/or its its Affiliates,  
All Rights Reserved  

### 1. Build MIO Library
Uncompress the source code package.  
Run the following steps to build MIO library and examples.
```
./autogen.sh
./configure
```  

If you install Motr rpms in a customized directory such as your home
directory or build Motr from source, run configure like this:  

``./configure --with-libmotr=libmotr_directory --with-motr-headers= motr_headers_directory``

If you have Motr source and want to build MIO with the source code, run
configure as below

``./configure --with-motr-src=motr_source_directory``

3. Build MIO library

``make``

### 2. Run MIO examples  
MIO source code is shipped with varied examples.  

mio_rw_threads starts specified number of READ/WRITE threads. WRITE threads
create MIO objects and write data to them, and then inform the READ threads
to read the written objects. READ threads will verify the data fetched from
objects.  

Run mio_rw_threads following the steps below:  
#### 2.1. Start Motr (follow the Cortx-motr instruction)
#### 2.2. Change to directory `tests`

``cd tests``

MIO provides a sample configuration file *mio_config.yaml* in this directory.  
Edit *mio_config.yaml* to reflect Motr configurations in your system:  
replace the Motr client local address, MOTR_INST_ADDR, to yours.

<ol type="a">
  <li>replace the Motr client local address, MOTR_INST_ADDR, to yours.</li>
  <li>replace MOTR_HA_ADDR with your HA address.</li>
  <li>MOTR_PROFILE and MOTR_PROCESS_FID (usually no need to change these 2 parameters)</li>
  <li>replace the MOTR_USER_GROUP with the Motr user group in your system.</li>
  <li>replace the MOTR_USER_GROUP with the Motr user group in your system.</li>
  <li>To turn on ADDB for telemetry data generation, set MIO_TELEMETRY_STORE to
    ADDB.</li>
</ol>   

Methods to get the above parameters:
<ol type="a">
  <li>hctl motr status</li>
  <li>checkout /etc/motr/sys-*/conf.xc to search strings for M0_CST_HA</li>
  <li>if your system is shipped with Motr sample apps, run:  

  `` motr-sample-app-dir/scripts/c0appzrcgen ``
  </li>
  </ol>
  
#### 2.3. Add your account to Motr group then run mio_rw_thread or run it as root:  

```
sudo ../examples/mio_rw_threads --help  

Usage of mio_rw_thread
  -o, --object         FID       Starting object ID
  -n, --nr_objs        INT       The number of objects
  -s, --block-size     INT       block size in bytes or with suffix b/k/m/g/K/M/G
  -c, --block-count    INT       number of blocks written to an object with suffix b/k/m/g/K/M/G
  -t, --threads                  Number of threads
  -y, --mio_conf                 MIO YAML configuration file
  -h, --help                     shows this help text and exit
```

All MIO examples use 2 uint64_t to represent an object ID, example of running `mio_rw_threads`:
``sudo ../examples/mio_rw_threads -s 4096 -c 1 -n 10 -t 1 -y ./mio_config.yaml -o 1:12346800``  


#### 2.4. Run all MIO tests
MIO also provides with test scripts to test basic functionalities.  
Run the scripts as root or users of Motr group as above.  
``sudo ./mio_run_tests.sh``  

#### 2.5. Quick Tests on Sage platform  
```
>>cd gitlab/mio/  
>>git pull
>>./autogen.sh  
>>./configure  
>>make  
>>cd tests/
>>motraddr.sh --mio > mio_config.yaml  
>>../examples/mio_rw_threads -s 4096 -c 1 -n 10 -t 1 -y ./mio_config.yaml -o 1:12346800   

a:12346800      7750c5e4c1549d15bdbc9690587f0c8b        7750c5e4c1549d15bdbc9690587f0c8b
9:12346800      cf99430f1feb3b7fa3eb03e396fc509a        cf99430f1feb3b7fa3eb03e396fc509a
8:12346800      f22766e0ae86c0d0777747835b749390        f22766e0ae86c0d0777747835b749390
7:12346800      23ba669b5ac4478682f273a13f5db5d6        23ba669b5ac4478682f273a13f5db5d6
6:12346800      528f642a32ef56c36f987049ac42e46c        528f642a32ef56c36f987049ac42e46c
5:12346800      d8b5aea301d4e653e7ebd610fcf56a2f        d8b5aea301d4e653e7ebd610fcf56a2f
4:12346800      d6f35954ae4a33961d1e8809000f4d59        d6f35954ae4a33961d1e8809000f4d59
3:12346800      e302c06452fd87119c996fa911373894        e302c06452fd87119c996fa911373894
2:12346800      a133a792799f49fe7807c34fc25d393c        a133a792799f49fe7807c34fc25d393c
1:12346800      f3a95aeb5b0005ca305999bac7a75577        f3a95aeb5b0005ca305999bac7a75577
[Final Report]  Objects TODO: 10        Completed: 10   Failed: 0       Matched: 10
```  

Note: m0composite needed for composite object tests. needs to be runs only once. Run it anyway and ignore failures.

```
>>motraddr.sh --exp > out.txt
>>cat out.txt
# umanesan1 client-22
# Bash shell export format
export CLIENT_LADDR="172.18.1.22@o2ib:12345:4:8"
export CLIENT_HA_ADDR="172.18.1.21@o2ib:12345:1:1"
export CLIENT_PROFILE="0x7000000000000001:0x4dc"
export CLIENT_PROC_FID="0x7200000000000001:0x151"
>>source ./out.txt
>>m0composite "$CLIENT_LADDR" "$CLIENT_HA_ADDR" "$CLIENT_PROFILE" "$CLIENT_PROC_FID"

motr[26256]:  7bd0  ERROR  [dix/req.c:794:dix_idxop_meta_update_ast_cb]  All items are failed
motr[26256]:  5bd0  ERROR  [dix/req.c:794:dix_idxop_meta_update_ast_cb]  All items are failed
```    

Create MIO configuration yaml files for tests:
```
>>motraddr.sh --mio > mio_config_p1.yaml
>>motraddr.sh --mio > mio_config_p2.yaml
>>motraddr.sh --mio > mio_config_p3.yaml
>>cat *.yaml | grep MOTR_INST_ADDR
  MOTR_INST_ADDR: 172.18.1.22@o2ib:12345:4:10
  MOTR_INST_ADDR: 172.18.1.22@o2ib:12345:4:14
  MOTR_INST_ADDR: 172.18.1.22@o2ib:12345:4:3
  MOTR_INST_ADDR: 172.18.1.22@o2ib:12345:4:8
>>../examples/mio_comp_obj_example -o 1:123456 -y ./mio_config.yaml
>>./mio_run_tests.sh
/home/users/jusers/umanesan1/sage/gitlab/mio/tests
/home/users/jusers/umanesan1/sage/gitlab/mio
/home/users/jusers/umanesan1/sage/gitlab/mio/examples/  
```  

MIO tests report if all the tests run successfully:
```
MIO tests starts:  
Test log will be stored in:  
/home/users/jusers/umanesan1/sage/gitlab/mio/tests/mio_test_sandbox_2021-01-06-12-48-22/mio_test_2021-01-06-12-48-22.log
[T1] Object creation and deletion tests
        io_create_delete_test:  passed
[T2] Object IO tests
        io_test_with_sizes_of_multi_4KB:  passed
        io_test_with_sizes_of_multi_non_4KB:  passed
        io_test_in_async:  passed
        io_test_obj_rw_with_lock:  passed
        io_test_with_multi_threads:  passed
        io_test_with_multi_procs:  passed
[T3] KVS tests
        kvs_create_query_delete_test:  passed
[T4] Composite object tests
        comp_obj_test:  passed
[T5] Object hint tests
        obj_hint_test:  passed
mio_run_tests: test status: SUCCESS
```
