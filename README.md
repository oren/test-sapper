# Test Sapper

## 1. The problem:
refresh http://localhost:5000/food/export shows 404

## 2. Step to Reproduce:

```
git clone git@github.com:oren/test-sapper.git
cd test-sapper
npm install
npx sapper export --basepath /food
npx serve __sapper__/export/
```

## 3. Note:
The problem disappear if I remove `'/food',` line from server.js and run `npx sapper export`.
