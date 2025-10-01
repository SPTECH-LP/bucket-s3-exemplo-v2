## 🔑 Autenticando com o Default Credentials Provider

O **AWS SDK for Java 2.x** utiliza, por padrão, uma **cadeia de provedores de credenciais** (
*default credentials provider chain*).
Essa cadeia procura automaticamente pelas credenciais configuradas no ambiente, seguindo a ordem
abaixo:

---

### 1. **Propriedades do Sistema Java**

* **Provedor**: `SystemPropertyCredentialsProvider`
* Carrega credenciais a partir das propriedades definidas em tempo de execução na JVM:

  ```properties
  aws.accessKeyId=SEU_ACCESS_KEY
  aws.secretAccessKey=SEU_SECRET_KEY
  aws.sessionToken=SEU_SESSION_TOKEN
  ```

> 💡 **Dica**: veja como definir propriedades do sistema Java
> na [documentação oficial](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html).
>

---

### 2. **Variáveis de Ambiente**

* **Provedor**: `EnvironmentVariableCredentialsProvider`
* Carrega credenciais a partir das variáveis de ambiente.
* Você pode definir na IDE

> **Dica:** Você pode utilizar o IntelliJ para setar as variáveis de ambiente.
> <table> 
> <tr>
> <td align="center">
> <img width="300" alt="image" src="https://github.com/user-attachments/assets/10a6f113-de19-4f7e-9b39-4c579f55a639" />
> </td>
> <td align="center">
> <img width="300" alt="image" src="https://github.com/user-attachments/assets/95380179-1860-4a60-89f5-4f27903bf786" />
> </td>
> <td align="center">
> <img width="300" alt="image" src="https://github.com/user-attachments/assets/e1019e27-7756-43f3-9fb3-4c96f1f857aa" />
> </td>
> </tr>

> </table>

* ou no terminal:

  ```bash
  export AWS_ACCESS_KEY_ID=SEU_ACCESS_KEY
  export AWS_SECRET_ACCESS_KEY=SEU_SECRET_KEY
  export AWS_SESSION_TOKEN=SEU_SESSION_TOKEN
  ```

---

### 3. **Token de Identidade Web (STS)**

* **Provedor**: `WebIdentityTokenFileCredentialsProvider`
* Obtém credenciais temporárias via **AWS STS** (Security Token Service), com base em:

    * Propriedades do sistema Java, ou
    * Variáveis de ambiente

---

### 4. **Arquivos de Configuração Compartilhados**

* **Provedor**: `ProfileCredentialsProvider`
* Carrega credenciais do IAM Identity Center (SSO) ou perfis locais definidos em:

    * `~/.aws/credentials`
    * `~/.aws/config`

Exemplo de perfil `default` no arquivo `credentials`:

```ini
[default]
aws_access_key_id=SEU_ACCESS_KEY
aws_secret_access_key=SEU_SECRET_KEY
```

> 💡 **Dica**: esses arquivos são **compartilhados entre diferentes SDKs e ferramentas da AWS**.
> Consulte
>
o [formato dos arquivos de configuração](https://docs.aws.amazon.com/sdkref/latest/guide/file-format.html).

---

### 5. **Credenciais de Container (Amazon ECS)**

* **Provedor**: `ContainerCredentialsProvider`
* Obtém credenciais de variáveis já definidas pelo ECS:

    * `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` ou `AWS_CONTAINER_CREDENTIALS_FULL_URI`
    * `AWS_CONTAINER_AUTHORIZATION_TOKEN_FILE` ou `AWS_CONTAINER_AUTHORIZATION_TOKEN`

---

### 6. **Credenciais de Instância EC2**

* **Provedor**: `InstanceProfileCredentialsProvider`
* Obtém credenciais automaticamente pelo **serviço de metadados da instância EC2**.

---

## Conclusão ✅

Essa cadeia garante que, na maioria dos casos, não seja necessário configurar credenciais
diretamente no código. O SDK escolhe automaticamente a melhor opção de acordo com o ambiente onde a
aplicação está rodando:

- Em uma **IDE**, por exemplo, ele pode usar as **variáveis de ambiente** (passo 2).

- Em uma **instância EC2**, a autenticação é feita automaticamente pelo **serviço de metadados**,
  sem nenhuma configuração adicional.

Veja mais
em [AWS: Default credentials](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/credentials-chain.html)
