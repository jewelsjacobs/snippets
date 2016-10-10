#Test for insecure links in webpage

```
#!/bin/bash
curl -o test.html https://stage.homeaway.com/destination/virginia-beach
ISTHERE=`cat test.html | grep "http[^s]"`
if [ "$ISTHERE" = "" ]; then
 echo "test passed"
else
 echo "test failed"
fi
```
