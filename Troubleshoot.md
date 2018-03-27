# Troubleshooting

## Can't launch VirtualBox
Detailed bug:

`$ virtualbox`

Response:

```
VirtualBox: Error -610 in supR3HardenedMainInitRuntime!
VirtualBox: dlopen("/usr/lib/virtualbox/VBoxRT.so",) failed: <NULL>

VirtualBox: Tip! It may help to reinstall VirtualBox.
```

Fix:
`sudo chown root:root /usr; sudo chown root:root /usr/lib`
