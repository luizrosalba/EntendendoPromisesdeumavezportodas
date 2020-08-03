## Curso entendendo promises de uma vez por todas 
Lucas Santos -> https://medium.com/trainingcenter/entendendo-promises-de-uma-vez-por-todas-32442ec725c2
###Parte 2 
Promises : Podemos fazer varias requisições em javascript para diversas páginas e isto pode nao ser respondido na hora 
poderíamos paralelizar essas promises realizando um código assíncrono : 

Código Assíncrono 
Requests HTTP 
Leitura de arquivos 
Acesso a serviços externos 
I/O 

Em um código assíncrono , o tempo total de execução é o tempo de execução da promisse mais lenta
pois todas estão executando em paralelo 

Funcionamento de uma promisse no node.js 
![](https://github.com/luizrosalba/EntendendoPromisesdeumavezportodas/blob/master/Capturar.PNG)

Loop infinito fica rodando recebendo as requisições e armazenando. Caso você precise ler um arquivo imenso , nao precisa parar a execução para lê-lo. 


Leitura Assíncrona : Le o poema inteiro e pega o buffer e imprime na tela 
```Javascript
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// This is an example of a sync file read, it pauses the program and reads the file
const content = fs.readFileSync(path.resolve(basePath, 'arquivo.txt'))

console.log(content.toString())

console.log('end')
```



```Javascript
  
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin\n')

// Take all files in the directory
const files = fs.readdirSync(path.resolve(basePath))
// Filter the ones we want
const sentences = files.filter((file) => /s[1-4].txt/gi.test(file))
//Read all files in order, synchronously
for (const file of sentences) {
  const sentence = fs.readFileSync(path.resolve(basePath, file)).toString()
  console.log(sentence, '\n')
}

console.log('end')

```

Callback : função que será executada apos outra função. Leitura assincrona . Perceber que ele executou begin (síncrono) , iniciou a leitura do texto (assíncrono) que é demorada , nao parou a execução , executou o end (síncrono) e somente quando terminou a leitura que imprimiu o texto 
```Javascript
  
  
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// This is an async fileRead process, it won't stop the program
// Notice the function in the end, that is a callback
fs.readFile(path.resolve(basePath, 'arquivo.txt'), { encoding: 'utf-8' }, (err, data) => {
  // This is the way we handle errors in callbacks
  if (err) throw err
  console.log(data)
})

console.log('End')
```
Utilizando uma função como callback 
```Javascript
  
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// This is an async fileRead process, it won't stop the program
// Notice the function in the end, that is a callback
fs.readFile(path.resolve(basePath, 'arquivo.txt'), { encoding: 'utf-8' }, cb)

console.log('End')

function cb (err, data) {
  // This is the way we handle errors in callbacks
  if (err) throw err
  console.log(data)
}
```
Lendo 4 arquivos de maneira assíncrona, cada execução trará um resultado diferente pois não podemos prever qual levará menos tempo para ler. Cada Arquivo terá um callback 
```Javascript
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// Take all files in the directory
const files = fs.readdirSync(path.resolve(basePath))
// Filter the ones we want
const sentences = files.filter((file) => /s[1-4].txt/gi.test(file))

//Read all files in order, synchronously
for (const file of sentences) {
  fs.readFile(path.resolve(basePath, file), { encoding: 'utf-8' }, cb)
}

console.log('End')

function cb (err, data) {
  if (err) throw err
  console.log(data)
}
```
Callback hell 
```Javascript
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// Take all files in the directory
fs.readFile(path.resolve(basePath, 's1.txt'), { encoding: 'utf-8' }, (err, data) => {
  cb(err, data)
  fs.readFile(path.resolve(basePath, 's2.txt'), { encoding: 'utf-8' }, (err, data) => {
    cb(err, data)
    fs.readFile(path.resolve(basePath, 's3.txt'), { encoding: 'utf-8' }, (err, data) => {
      cb(err, data)
      fs.readFile(path.resolve(basePath, 's4.txt'), { encoding: 'utf-8' }, (err, data) => {
        cb(err, data)
      })
    })
  })
})

console.log('End')

function cb (err, data) {
  if (err) throw err
  console.log(data)
}
```
callback hell um pouco mais ordenado 
```Javascript
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// Take all files in the directory
fs.readdir(path.resolve(basePath), (err, files) => {
  if (err) throw err
  files
    .filter((file) => /s[1-4].txt/gi.test(file)) // Filter the files
    .forEach((sentenceFile) => { // For each sentence, reads and prints (async)
      fs.readFile(path.resolve(basePath, sentenceFile), { encoding: 'utf-8' }, cb) // Will print unordered
    })
})

console.log('End')

// this callback is called everytime readFile executes
function cb (err, data) {
  if (err) throw err
  console.log(data)
}
```
CAllback recursivo 
```Javascript
const fs = require('fs')
const path = require('path')
const basePath = './assets/'

console.log('Begin')

// Take all files in the directory
start(1, 4)

console.log('End')

function start (index, max) {
  if (index > max) return
  fs.readFile(path.resolve(basePath, `s${index}.txt`), { encoding: 'utf-8' }, (err, data) => cb(err, data, index, max))
}

function cb (err, data, index, max) {
  if (err) throw err
  console.log(data)
  return start(index + 1, max)
}
```
Utilizando promises ao inves de callbacks . todo catch e then nao é uma promise propriamente dita mas retorna uma promise 
```Javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('End'), 5000)
})

console.log('Begin')
promise.then((text) => console.log(text)) 
// Or
promise.then(console.log)
// Or
promise.then(
  (res) => setTimeout(() => res('End'), 5000),
  (rej) => console.error(rej)
)
```
then = resolvido , catch = erro 

```Javascript
promise.then((res)=> {),(rej)=>{}) 
é o mesmo que 
promise.then((res)=> {)) 
promise.catch((rej)=>{}) 
```
A maioria das libs externas nao funciona com promisify 

```Javascript
  
const fs = require('fs')
const path = require('path')
const { promisify } = require('util')
const basePath = './assets/'

const readFileAsync = promisify(fs.readFile)

// Will render Begin and End first
console.log('Begin')
readFileAsync(path.resolve(basePath, 'invictus.txt'), { encoding: 'utf-8' })
  .then((data) => console.log(data))
  .catch((err) => console.error(err))
console.log('End')
```
Encadeamento de promises 
podemos encadear thens e catches 

```Javascript
const coinFlip = new Promise((resolve, reject) => Math.random() > 0.5 ? resolve(true) : reject(false))
/// 50% de chance de dar erro 
console.log('Begin')

// First case, if we have an error the last 'then' won't execute
coinFlip.then((data) => console.log('Yay 1'))
  .then(() => console.log('End1'))
  .catch((err) => console.error('Error in first case, then will not execute'))

// Second case, in an exception the last then will execute
coinFlip.then((data) => console.log('Yay 2'))
coinFlip.catch((err) => console.error('Next then will execute'))
coinFlip.then(() => console.log('End2'))

// Same thing
coinFlip
  .then((success) => console.log('Yay 3'), (fail) => console.log('Error in third case'))
  .then(() => console.log('End3'))
```
Cada catch só vale pra um then; quando ocorre um erro no then ele percorre todos os outros até encontrar o primeiro catch 
```Javascript

// Second case, in an exception the last then will execute
coinFlip.then((data) => console.log('Yay 2'))
coinFlip.catch((err) => console.error('Next then will execute'))
coinFlip.then(() => console.log('End2'))

// Same thing
coinFlip
  .then((success) => console.log('Yay 3'), (fail) => console.log('Error in third case'))
  .then(() => console.log('End3'))
```

then = garçom (pode ter dois ) e catch seria outro pedido. A execução não para 
```Javascript
const coinFlip = new Promise((resolve, reject) => Math.random() > 0.5 ? resolve(true) : reject(false))

console.log('Begin')

coinFlip.then((data) => console.log(data))
  .catch((err) => { throw err })
  .then(() => console.log('End1'))

new Promise((resolve) => setTimeout(() => resolve(), 2000)).then(() => console.log('Yay'))
```
Multiple catches 
```Javascript
const coinFlip = new Promise((resolve, reject) => Math.random() > 0.5 ? resolve(true) : reject(false))

console.log('Begin')

// First catch will capture error from first then, last catch will capture errors from next then
coinFlip.then((data) => console.log('Yay 1'))
  .catch(() => console.log('First catch'))
  .then(() => console.log('Result'))
  .catch((err) => console.error('Error in last then'))
  .then(() => console.log('End1'))


// // Both catches will be executed
coinFlip.then((data) => console.log('Yay 1'))
  .then(() => console.log('Result'))
  .then(() => console.log('End1'))

coinFlip.catch(() => console.log('First catch'))
  .catch((err) => console.error('Error in first case, next then will not execute'))


// Both catches will be executed
coinFlip.then((data) => console.log('Yay 1'))
  .catch((err) => console.error('Catch after then'))
  .then(() => console.log('Result'))
  .then(() => console.log('End1'))

coinFlip.catch(() => console.log('First catch'))

// Catch from coinFlip will execute only when the first then fails, second catch will always be executed
coinFlip.then((data) => console.log('Yay 1'))
  .then(() => { throw new Error() })
  .catch((err) => console.error('Error in last then'))
  .then(() => console.log('End1'))

coinFlip.catch(() => console.log('First catch'))
```


```Javascript
const coinFlip = new Promise((resolve, reject) => setTimeout(() => Math.random() > 0.5 ? resolve(true) : reject(false), 2000))
let p = Promise.resolve('Yay').then(coinFlip) /// coinflip nao eh funcao eh uma promise 
/// Promise.resolve retorna uma promise 

// P will resolve immediately to YAY, but will not be settled until the second promise resolves with the coinflip
p.then(console.log).catch(() => console.log('error')) /// resolve a primeira depois espera 2 s e resolve a segunda 
```
Finally Será sempre executando dando erro ou nao 
```Javascript
const fs = require('fs')
const path = require('path')
const { promisify } = require('util')
const basePath = './assets/'

const readFileAsync = promisify(fs.readFile)

console.log('Begin')
readFileAsync(path.resolve(basePath, 'invictus.txt'), { encoding: 'utf-8' })
  .then((data) => console.log(data))
  .catch((err) => console.error(err))
  .finally(() => console.log('End'))
```
Promise hell , encadeamento de promises 

```Javascript
const fs = require('fs')
const path = require('path')
const { promisify } = require('util')
const basePath = './assets/'

const readFileAsync = promisify(fs.readFile)

console.log('Begin')
readFileAsync(path.resolve(basePath, 's1.txt'), { encoding: 'utf-8' })
  .then((sentence) => {
    console.log(sentence, '\n')
    return readFileAsync(path.resolve(basePath, 's2.txt'), { encoding: 'utf-8' })
  })
  .then((sentence) => {
    console.log(sentence, '\n')
    return readFileAsync(path.resolve(basePath, 's3.txt'), { encoding: 'utf-8' })
  })
  .then((sentence) => {
    console.log(sentence, '\n')
    return readFileAsync(path.resolve(basePath, 's4.txt'), { encoding: 'utf-8' })
  })
  .then(console.log)
  .finally(() => console.log('End'))
```

Melhorando o promise hell 
```Javascript
const fs = require('fs')
const path = require('path')
const { promisify } = require('util')
const basePath = './assets/'

const readFileAsync = promisify(fs.readFile)

function read (index) {
  return readFileAsync(path.resolve(basePath, `s${index}.txt`), { encoding: 'utf-8' })
}

function start (index, max) {
  if (index > max) return
  read(index).then((data) => {
    console.log(data, '\n')
    start(index + 1, max)
  })
}

start(1, 4)
```

Promise.all resolve todas as promises 
Se qquer uma falhar vai dar erro em todas e retornara apenas o 1º catch 

```Javascript
const fs = require('fs')
const path = require('path')
const { promisify } = require('util')
const basePath = './assets/'

const readFileAsync = promisify(fs.readFile)

function read (index) {
  return readFileAsync(path.resolve(basePath, `s${index}.txt`), { encoding: 'utf-8' })
}

console.log('Begin')
const promiseArray = []
for (let i = 1; i <= 4; i++) promiseArray[i - 1] = read(i) // Arrays start at 0, so if we are populating index 1, the 0 will be undefined

Promise.all(promiseArray).then(console.log)
```

