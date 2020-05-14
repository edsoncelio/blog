---
title: "Git Aliases"
date: 2019-08-24
draft: false
categories: ["dev", "devops", "2019"]
tags: ["git"]
---

Para quem usa linux (powershell também tem algo similar) sabe que usar alias facilita muito a vida, 
porque basicamente damos um apelido para comandos que usamos frequentemente, e no git é a mesma coisa.


## Uso do comando e exemplos

* Dando o apelido st para o comando `git status` :

	`$ git config --global alias.st status`

  Após isso é só usar:

	`$ git st`


* Dando o apelido `last` para o comando `git log -1 HEAD` (ver o último commit):

	`$ git config --global alias.last 'log -1 HEAD'`

  Após isso, é só usar:
	
	`$ git last`
	

Dessa forma, é possível criar apelidos para todos os comandos que usa com mais frequência!

#### Referências:

* [Documentação Oficial (en) - Git Aliases](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases)
