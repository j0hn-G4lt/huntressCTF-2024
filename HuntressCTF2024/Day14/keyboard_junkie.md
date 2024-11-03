## Keyboard Junkie

Use [this](https://github.com/TeamRocketIst/ctf-usb-keyboard-parser) to extract keyboard data from pcap files. Follow the directions they give, utilizing tshark and the provided python script `usbkeyboard.py`. 
```bash
tshark -r ./keyboard_junkie -Y 'usb.capdata && usb.data_len == 8' -T fields -e usb.capdata | sed 's/../:&/g2' > data.txt

python3 usbkeyboard.py ./data.txt

so the answer is flag{f7733e0093b7d281dd0a30fcf34a9634} hahahah lol
```
