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
```Javascript

```
```Javascript

```
```Javascript

```
```Javascript

```
```Javascript

```
```Javascript

```
```Javascript

```


