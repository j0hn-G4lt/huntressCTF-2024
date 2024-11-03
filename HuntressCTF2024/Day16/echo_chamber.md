## Echo Chamber
Given a pcap file with quite a lot to sort through, lets filter the ICMP data out since we are trying to see what John was trying to send himself. Can use tshark to sort through this for us.
```sh
tshark -r echo_chamber.pcap -Y "icmp" -T fields -e data | \
awk '{print substr($0, 1, 2)}' | \
xargs -I {} printf "\x{}"
```
Which will eventually extract at the very end an echoed flag with duplicates.
```
OOxx..ttEEXXttccaappttiioonnffllaagg{{66bb3388aaaa991177aa775544dd88bbff338844ddcc7733ffddee663333aadd}}ssDD��ttEEXXttccaappttiioonn::lliinneess11��==��IIEENNDD��BB``�� 
``` 
Clean it up by removing duplicates and you get: `flag{6b38aa917a754d8bf384dc73fde633ad}`
