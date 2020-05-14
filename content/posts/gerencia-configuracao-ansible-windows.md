---
title: "Gerência de configuração de máquinas Windows com Ansible"
date: 2019-04-28
draft: false
categories: ["infra", "2019", "devops"]
tags: ["ansible", "windows"]
---

Nesse artigo vou contar um pouco da minha experiência com gerência de configuração de máquinas Windows usando o Ansible, 
pode ser que sirva para alguém que esteja começando a trilhar esse caminho tortuoso.


## Não se usa SSH, e sim WinRM

O primeiro ponto é que em hosts Windows se usa protocolo chamado [Windows Remote Management](https://docs.microsoft.com/en-us/windows/desktop/winrm/portal) 
(baseado em SOAP) que deve ser configurado previamente no host.

Felizmente a documentação do Ansible fornece um script para fazer toda a configuração, nesse [link](https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html#winrm-setup).

**observação:** recomendo fazer todos os testes que a documentação sugere, porque se tiver alguma chance de alguma dar errado, com certeza vai dar!

## É necessário instalar a biblioteca pywinrm no Ansible
Dado que o Ansible comunica com hosts windows usando o winrm, é necessário instalar uma biblioteca que não vem por padrão no Ansible, 
é bem simples (é python :D):

`pip3 install "pywinrm>=0.3.0"`


## Configuração dos hosts no inventário
Com o winrm configurado corretamente, é hora de configurar efetivamente a conexão com os hosts do inventário, o que requer um pouco de atenção.

Segue um exemplo de inventário com uma máquina windows:

```
[win]
192.168.2.10 

[win:vars]
ansible_user=meu_windows
ansible_password=minha_senha_do_windows_s2
ansible_connection=winrm
ansible_winrm_server_cert_validation=ignore
```

Como podem ver, tem duas variável referentes a conexão com o Winrm: a primeira define que o tipo de conexão não é ssh e sim o winrm, 
e a segunda se refere a não validação de certificados dado que para esse caso estamos autenticando via usuário e senha.

Além dessas opções existem outras que vale a pena comentar:

`ansible_winrm_transport`

Que pode ser : **basic**, **certificate**, **kerberos**, **ntlm** e **credssp** (isso daria um artigo inteiro, então vou deixar o link da documentação nas referências).

`ansible_winrm_scheme`

Que pode ser: http ou https (por padrão é usado https que usa a porta 5986, http usa a porta 5985)


Além dessas opções existem muitas outras, que podem ser usadas dependendo do caso, todas as opções podem ser vistas na documentação também, nesse [link](https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html#inventory-options).

## Executando módulos Windows no Ansible
Finalmente, se tudo tiver sido configurado com sucesso, é possível comunicar com seu host windows, 
fazendo um teste com o ping por exemplo:

`ansible win -i hosts -m win_ping`

**detalhes:** no meu caso o meu grupo de hosts é o **win**, e usei o módulo ping **win_ping**!

Uma lista com todos os módulos do Ansible disponíveis para Windows pode ser vista na documentação também, nesse [link](https://docs.ansible.com/ansible/latest/modules/list_of_windows_modules.html).

Bom, finalmente o seu host Windows já está configurado e já é possível automatizar boa parte da gerência de configuração.

### Links para referência

* [Connecting to a Windows host (how to no blog do Ansible)](https://www.ansible.com/blog/connecting-to-a-windows-host)
* [Windows Remote Management (documentação)](https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html)
* [Manage Windows like Linux with Ansible (talk apresentada no Red Hat Summit 2018)](https://www.youtube.com/watch?v=FEdXUv02Dbg)
