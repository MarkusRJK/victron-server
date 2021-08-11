### Features:

- install and use async-mutex in communication with Victron device
- optional read from cache
- create test script for index.js using serial port's mock
- check spec of the following addresses:
  get('0xED7D'); // 'VS'; response: -1 or FFFF
  get('0xED8C'); // 'I'; existiert nicht - Error flag 01 unknown ID
  get('0xEDEC'); // 'T'; response 65535 or FFFF, bmvdata = null
  get('0x0309'); // H10 was 0 test again
  get('0x030A'); // H11 was 0 test again
  get('0x030B'); // H12 was 0 test again
- implement get/set for
  get('0xEEFF'); // 'CE' -ok ??
  set('0x0FFE'); // 'TTG'; ok
  set('0xEEB6'] = 'SOC' - ok
  set('0x0300'); // H1 - ok
  set('0x0301');   // H2 - ok
  set('0x0302'); // H3 - ok
  set('0x0303'); // H4 - ok
  set('0x0304'); // H5 - ok
  set('0x0305'); // H6 - ok
  set('0x0306'); // H7 - ok
  set('0x0307'); // H8 - ok
  set('0x0308'); // H9 - ok
  set('0x030E'); // H15 - ok
  set('0x030F'); // H16 - ok
  set('0x0310'); // H17 - ok
  set('0x0311'); // H18 - ok
  get('0x0100'); // product id (ro) - long string with 0000...00402FE
  get('0x0101'); // product revision (ro) - ERROR: 01 i.e. unknown; only for BMV-712
  get('0x010A'); // serial no (ro) - ok - nicht decodiert
  get('0x010B'); // model name (ro) - how to read? all hex: 70B0100424D562D37303297
  get('0x010C'); // description - ERROR: 01; only for BMV-712
  get('0x0120'); // device uptime (ro)
  get('0x0150'); // bluetooth capabilities - ERROR 01
- Implement ES6 syntax for VictronEnergyDevice


### Fixes:

- Error: No such file or directory, cannot open /dev/serial/by-id/...
  cope with unplugged cable:
    events.js:183
      throw er; // Unhandled 'error' event
      ^
    Error: Error: No such file or directory, cannot open /dev/serial/by-id/usb-VictronEnergy_BV_VE_Direct_cable_VE1SUT80-if00-port0
- message times out and is removed from responseMap but then may still arrive
  (not being found in repsonseMap -> timedoutMap)
- handle initialization of cache values
- several times seen: "Device refused to set expected value:" for 84E03 (set Relay)
  maybe needs reset of BMV
- 84E03 (set relay ON/OFF): repeating the command may sent 84e03 to device, receive repsonse,
  created response, clear timeout and then suddenly send the same command again despite command
  compression
- "unwarranted command id: 4 received - ignored": this error comes with 4000051. Therefore
  create response for 4000051 if received - 40000 is successful restart ==> after restart cmd,
  wait till 40000 comes in!!! then continue firing commands! 
  but log protocol ended up in 
  

    

