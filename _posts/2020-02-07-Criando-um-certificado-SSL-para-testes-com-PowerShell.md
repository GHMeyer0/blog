##### Artigo escrito por [Rodolfo Fadino Jr](https://rodolfofadino.com.br/@RodolfoFadino) e esta disponivel neste [link](https://rodolfofadino.com.br/criando-um-certificado-ssl-para-testes-com-powershell-bb6aa56b5cff)
---
Recentemente estava trabalhando com alguns desenvolvimentos e testes que necessitavam de um certificado SSL em meu ambiente de desenvolvimento. Neste post vou mostrar como emitir um certificado com PowerShell, e adicionar ele como confiável em nosso computador.

Cenário
-------

No meu ambiente eu tenho uma entrada no hosts configurada para o domínio que estava utilizando como testes ser respondido pelo meu localhost:

C:\Windows\System32\drivers\etc\hosts

127.0.0.1 [www.dominioteste.com.br](http://www.dominioteste.com.br/)

![image](https://miro.medium.com/max/30/0*1M7N_FLUVXHCAjOy.png?q=20)

![image](https://miro.medium.com/max/644/0*1M7N_FLUVXHCAjOy.png)

Além disto, utilizava um certificado auto assinado que tinha sido gerado pelo IIS, porém mesmo após "confiar" no certificado pelo meu navegado, ele ainda estava apresentando erros no navegador:

![image](https://miro.medium.com/max/30/0*FdGAR96GpYVNIPgm.png?q=20)

![image](https://miro.medium.com/max/644/0*FdGAR96GpYVNIPgm.png)

Este erro se dá, principalmente pois ao emitir um certificado auto assinado pelo IIS ele emite para o nome completo do computador, e não para o domínio que estamos utilizando de teste.

![image](https://miro.medium.com/max/30/0*mHwbgrRwNM7hGoVl.png?q=20)

![image](https://miro.medium.com/max/644/0*mHwbgrRwNM7hGoVl.png)

Emitindo um certificado com PowerShell
--------------------------------------

Para emitir um certificado vamos utilizar precisar abrir uma instancia de PowerShell e utilizar o seguinte comando:

New-SelfSignedCertificate -certstorelocation Cert:\CurrentUser\My -dnsname [www.dominioteste.com.br](http://www.dominioteste.com.br/)

![image](https://miro.medium.com/max/30/0*eX6-81n7t_tgrb6y.png?q=20)

![image](https://miro.medium.com/max/644/0*eX6-81n7t_tgrb6y.png)

Vamos copiar o Thumbprint e utilizar ele para exportar o certificado.

Após copiar o Thumbprint, precisamos criar uma senha para o certificado que vamos exportar:

$pwd = ConvertTo-SecureString -String "minhasenha" -Force -AsPlainText

Com a senha criada, podemos exportar o certificado com o seguinte comando:

Export-PfxCertificate -cert Cert:\CurrentUser\My\96F42D55959FD4EE82EEFF66B758CBAD1FF2AC37 -FilePath c:\temp\www.dominioteste.com.br.pfx -Password $pwd

![image](https://miro.medium.com/max/30/0*MgWY4hzdAHjDkIJe.png?q=20)

![image](https://miro.medium.com/max/644/0*MgWY4hzdAHjDkIJe.png)

Feito isso temos nosso certificado pronto, agora vamos adicionar ele no IIS.

Basta abrir o IIS, e no nível de servidor utilizar a opção Certificados do Servidor:

![image](https://miro.medium.com/max/30/0*D7Ekp9UWE9pksDwD.png?q=20)

![image](https://miro.medium.com/max/572/0*D7Ekp9UWE9pksDwD.png)

E nele utilizar a opção importar:

![image](https://miro.medium.com/max/30/0*DU4atGiVSppZDhp8.png?q=20)

![image](https://miro.medium.com/max/620/0*DU4atGiVSppZDhp8.png)

Após ter importado nosso certificado, podemos configurar ele no website:

![image](https://miro.medium.com/max/30/0*AD65sqm2qKkCsMzr.png?q=20)

![image](https://miro.medium.com/max/644/0*AD65sqm2qKkCsMzr.png)

O último passo que será necessário é adicionar nosso certificado na pasta de confiáveis no nosso computador, para isto basta abrir a parte de gerenciamento de certificados do computador:

//run certmgr.msc

![image](https://miro.medium.com/max/30/0*VRq9GY7XWTvJ7T2v.png?q=20)

![image](https://miro.medium.com/max/335/0*VRq9GY7XWTvJ7T2v.png)

E importar o certificado dentro da pasta "Autoridades de Certificação Raiz Confiáveis"

![image](https://miro.medium.com/max/30/0*-GnOSUAAdJ9qHgsA.png?q=20)

![image](https://miro.medium.com/max/644/0*-GnOSUAAdJ9qHgsA.png)

![image](https://miro.medium.com/max/30/0*LoSs9ZwPhO5mjiBG.png?q=20)

![image](https://miro.medium.com/max/530/0*LoSs9ZwPhO5mjiBG.png)

Após importar, ao acessar nosso site novamente teremos o certificado funcionando 100% (as vezes é necessário reiniciar o computador) \o/

![image](https://miro.medium.com/max/30/0*qkq-xUetxHhQuA6s.png?q=20)

![image](https://miro.medium.com/max/644/0*qkq-xUetxHhQuA6s.png)

![image](https://miro.medium.com/max/30/0*HA_G84B90c-t_C7g.png?q=20)

![image](https://miro.medium.com/max/598/0*HA_G84B90c-t_C7g.png)

Em resumo, estes são os comandos para criar e exportar o certificado, lembrando que o Thumbprint muda

![Alegre](https://miro.medium.com/max/30/0*xLHJuWtjlKg4sUvW.png?q=20)

![Alegre](https://miro.medium.com/max/19/0*xLHJuWtjlKg4sUvW.png)

```
New-SelfSignedCertificate -certstorelocation Cert:\CurrentUser\My -dnsname www.dominioteste.com.br

##96F42D55959FD4EE82EEFF66B758CBAD1FF2AC37

$pwd = ConvertTo-SecureString -String "minhasenha" -Force -AsPlainText

Export-PfxCertificate -cert Cert:\CurrentUser\My\96F42D55959FD4EE82EEFF66B758CBAD1FF2AC37 -FilePath c:\temp\www.dominioteste.com.br.pfx -Password $pwd

```
