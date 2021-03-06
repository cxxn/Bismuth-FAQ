# Bootstrapping Node and/or Hypernode

Bootstrapping allows you to get sync faster, with an up to date ledger from a safe source.

In order to bootstrap, the node or HN must be off, so you have to deactivate the cron sentinels first.  
We'll explain that.

The following instructions apply to the default auto install scheme.

## A. Node Bootstrap

### A1. Stop the node sentinel cron

`crontab -e`

If you are asked
```
Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/vim.tiny
  4. /bin/ed

Choose 1-4 [1]:
```
Select 1 for nano. You'll have to tell that only once.

You should then see  
```
* * * * * cd /root/Bismuth;python3 node_sentinel.py
* * * * * cd /root/hypernode/crontab;python3 cron1.py
*/5 * * * * cd /root/hypernode/crontab;python3 cron5.py
```

Comment out the node sentinel, so it reads  
```
#* * * * * cd /root/Bismuth;python3 node_sentinel.py
* * * * * cd /root/hypernode/crontab;python3 cron1.py
*/5 * * * * cd /root/hypernode/crontab;python3 cron5.py
```

ctrl-x, yes to save, enter

### A2. Stop the node

Clean stop request:  
`cd /root/Bismuth`  
`python3 commands.py stop`

check the node stopped:
`screen -x node`  
If there is no such screen, node is stopped.  
If not, you'll be in the node screen, it can use a few sec to stop.  
In case it does not want to stop clean, ctrl-c it, that will also close the screen.

### A3. Get the helper

`cd /root/Bismuth`  
`wget https://raw.githubusercontent.com/bismuthfoundation/util/master/snapshot_download.py`  
`python3 snapshot_download.py`

Then follow the instructions.  
This will fetch and verify the bootstrap

### A4. Reactivate the node sentinel

`crontab -e`

Uncomment the node_sentinel line, so it reads again  
```
* * * * * cd /root/Bismuth;python3 node_sentinel.py
* * * * * cd /root/hypernode/crontab;python3 cron1.py
*/5 * * * * cd /root/hypernode/crontab;python3 cron5.py
```

The node will restart within a minute.

## B. HyperNode Bootstrap

### B1. Stop the hypernode sentinel cron

`crontab -e`

You should have  
```
* * * * * cd /root/Bismuth;python3 node_sentinel.py
* * * * * cd /root/hypernode/crontab;python3 cron1.py
*/5 * * * * cd /root/hypernode/crontab;python3 cron5.py
```

Comment out the hypernode sentinel1, so it reads  
```
* * * * * cd /root/Bismuth;python3 node_sentinel.py
#* * * * * cd /root/hypernode/crontab;python3 cron1.py
*/5 * * * * cd /root/hypernode/crontab;python3 cron5.py
```

ctrl-x, yes to save, enter

### B2. Stop the hypernode

`screen -x hypernode`  
If there is no such screen, hypernode is not running.  
If you see the HN output, ctrl-c it, that will also close the screen.

### B3. Delete the PoS Ledger

`rm /root/hypernode/main/data/poc_pos_chain.*`  

Hypernode will auto fetch a new one at start

### B4. Reactivate the hypernode sentinel

`crontab -e`

Uncomment the hypernode sentinel line, so it reads again  
```
* * * * * cd /root/Bismuth;python3 node_sentinel.py
* * * * * cd /root/hypernode/crontab;python3 cron1.py
*/5 * * * * cd /root/hypernode/crontab;python3 cron5.py
```

The hypernode will restart within a minute, bootstrap and restart.


## C. Refilling Node peers

The node can occasionally tag too many peers as inactive, and left you with too small a list.

### C1. Stop the node

Follow A1, A2.  
Node should be stopped now

`cd /root/Bismuth`  
`rm peers.txt;wget https://raw.githubusercontent.com/bismuthfoundation/Bismuth/postfork-rc2/peers.txt`  
`rm suggested_peers.txt;wget https://raw.githubusercontent.com/bismuthfoundation/Bismuth/postfork-rc2/suggested_peers.txt`

Reactivate node sentinel, by following A4
