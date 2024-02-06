# Curl

## Samples

1. Show headers `curl -I -v  https://my.site.com`
2. See Cookie settings: `curl -v -k "https://api.local/v1/user/login?email="`
3. POST file: `curl -X POST --data-binary @path/to/my-file.txt`
4. POST JSON parameter: `curl -s -X POST http://127.0.0.1:3000/workflows -d "name=test-workflow"`  
