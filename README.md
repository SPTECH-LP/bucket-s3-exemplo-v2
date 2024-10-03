## Exemplo de Integração com Amazon S3 usando Java

### Visão Geral

Este exemplo demonstra como interagir com o serviço Amazon S3 usando a SDK da AWS para Java. O Amazon S3 é um serviço de armazenamento de objetos e é amplamente utilizado para armazenar e recuperar dados.

### Dependências

Adicione as dependências ao arquivo `pom.xml` do seu projeto para interagir com o serviço Amazon S3:

```xml
 <dependencies>
    <dependency>
        <groupId>software.amazon.awssdk</groupId>
        <artifactId>s3</artifactId>
        <version>2.27.21</version>
    </dependency>
</dependencies>
```

### Configurando o Client da AWS

A classe `S3Provider` será usada para criar o cliente que interage com o S3. As credenciais de acesso da AWS serão obtidas automaticamente por meio do ambiente ou de arquivos de configuração.

```java
public class S3Provider {

    private final AwsSessionCredentials credentials;

    public S3Provider() {
        this.credentials = AwsSessionCredentials.create(
                System.getenv("AWS_ACCESS_KEY_ID"),
                System.getenv("AWS_SECRET_ACCESS_KEY"),
                System.getenv("AWS_SESSION_TOKEN")
        );
    }

    public S3Client getS3Client() {
        return S3Client.builder()
                .region(Region.US_EAST_1)
                .credentialsProvider(() -> credentials)
                .build();
    }
}

```

#### Atributos

- **` private final AwsSessionCredentials credentials`**

Este atributo armazena as credenciais de sessão necessárias para autenticar as chamadas à API da AWS. As credenciais
consistem em uma chave de acesso, uma chave secreta e um token de sessão.

#### Métodos

- `S3Provider()`: Este é o construtor da classe. Ele é responsável por inicializar as credenciais da AWS. As credenciais são obtidas a partir das variáveis de ambiente:


- **`System.getenv("AWS_ACCESS_KEY_ID")`**: Recupera a chave de acesso da AWS.


- **`System.getenv("AWS_SECRET_ACCESS_KEY")`**: Recupera a chave secreta da AWS.


- **`System.getenv("AWS_SESSION_TOKEN")`**: Recupera o token de sessão

**System.getenv()** é usado para acessar as variáveis de ambiente do sistema operacional. As variáveis de ambiente devem ser configuradas com as credenciais da AWS antes de executar o código.


- `getS3Client()`: Este método é responsável por construir e retornar uma instância do cliente S3 (`S3Client`).

| Método                                   | Descrição                                                                                                     |
|------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `region(Region.US_EAST_1)`               | Define a região da AWS onde o cliente S3 operará. Neste caso, a região é "US East (Norte da Virgínia)".       |
| `credentialsProvider(() -> credentials)` | Configura o provedor de credenciais para o cliente S3. Usa uma função lambda para retornar as credenciais armazenadas no atributo `credentials`. |
| `build()`                                | Cria e retorna a instância configurada do cliente S3 (`S3Client`).                                            |
|`S3Client.builder()`:                     | Inicia a construção de um novo cliente S3 com as configurações desejadas.                                     |

### Operações com o Amazon S3


#### 1. Criar um Bucket:

```java
CreateBucketRequest createBucketRequest = CreateBucketRequest.builder()
        .bucket("nome-do-bucket")
        .build();

s3Client.createBucket(createBucketRequest);
```

- **`bucket("nome-do-bucket")`**: Define o nome único do bucket a ser criado.


- **`s3Client.createBucket()`**: Envia a requisição para criar o bucket.

#### 2. Listar Buckets:

```java
List<Bucket> buckets = s3Client.listBuckets().buckets();
for (Bucket bucket : buckets) {
    System.out.println("Bucket: " + bucket.name());
}
```

- **`listBuckets()`**: Lista todos os buckets existentes na conta S3.


- **`bucket.name()`**: Exibe o nome de cada bucket listado.

#### 3. Listar Objetos em um Bucket:

```java
ListObjectsRequest listObjects = ListObjectsRequest.builder()
        .bucket("nome-do-bucket")
        .build();

List<S3Object> objects = s3Client.listObjects(listObjects).contents();
for (S3Object object : objects) {
    System.out.println("Objeto: " + object.key());
}
```

- **`listObjects()`**: Lista todos os objetos armazenados dentro do bucket especificado.


- **`object.key()`**: Exibe a chave (identificador) de cada objeto no bucket.

#### 4. Fazer Upload de um Arquivo:

```java
PutObjectRequest putObjectRequest = PutObjectRequest.builder()
        .bucket("nome-do-bucket")
        .key(UUID.randomUUID().toString()) 
        .build();

s3Client.putObject(putObjectRequest, RequestBody.fromFile(new File("file.txt")));
```

- **`UUID.randomUUID().toString()`**: Gera um identificador único para o objeto no bucket.


- **`RequestBody.fromFile()`**: Converte um arquivo local para o formato apropriado para upload.

#### 5. Download de Arquivos:

```java
List<S3Object> objects = s3Client.listObjects(listObjects).contents();
for (S3Object object : objects) {
    GetObjectRequest getObjectRequest = GetObjectRequest.builder()
            .bucket("nome-do-bucket")
            .key(object.key())
            .build();

    InputStream objectContent = s3Client.getObject(getObjectRequest, ResponseTransformer.toInputStream());
    Files.copy(objectContent, new File(object.key()).toPath());
}
```

- **`getObjectRequest`**: Solicita o download de um objeto específico de um bucket.


- **`Files.copy()`**: Copia o conteúdo baixado para um arquivo local.

#### 6. Excluir um Objeto:

```java
DeleteObjectRequest deleteObjectRequest = DeleteObjectRequest.builder()
        .bucket("nome-do-bucket")
        .key("identificador-do-objeto")
        .build();

s3Client.deleteObject(deleteObjectRequest);
System.out.println("Objeto deletado: " + "identificador-do-objeto");
```

- **`deleteObject()`**: Exclui o objeto identificado pela chave dentro do bucket.


- **`key("identificador-do-objeto")`**: O identificador do objeto a ser deletado.
