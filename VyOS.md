# Connect by SSH via HOST-Only Adaptor
### Virtual Box
* Enable LAN3 as Host Only Adaptor

### VyOS
* Config
```
set interfaces ethernet eth2 address '169.254.153.1/16'
set interfaces ethernet eth2 description 'HOST-Only'
set interfaces ethernet eth2 hw-id '08:00:27:80:0f:e7'
```
