![readmes-2](https://github.com/user-attachments/assets/36f8bee4-8aa2-427f-859b-c07302b7f640)

## Exemplo de Integração com Amazon S3 usando Java

### Visão Geral 🎯

Esse exemplo demonstra como interagir com o serviço **Amazon S3** usando a **SDK da AWS** para Java.
O Amazon S3 é um serviço de armazenamento de objetos e é amplamente utilizado para armazenar e
recuperar dados.

### Dependências 📚️

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

### Configurando o Client da AWS ⚙️

A classe `S3Provider` será usada para criar o cliente que interage com o S3.

```java
public class S3Provider {

    private final AwsCredentialsProvider credentials;

    public S3Provider() {
        this.credentials = DefaultCredentialsProvider.create();
    }

    public S3Client getS3Client() {
        return S3Client.builder()
              .region(Region.US_EAST_1)
              .credentialsProvider(credentials)
              .build();
    }
}

```

#### Atributos

**` private final AwsSessionCredentials credentials`**

- Representa o provedor de credenciais usado pelo SDK.

#### Métodos

**`S3Provider()`**

- Construtor da classe.
- Inicializa o atributo `credentials` com o `DefaultCredentialsProvider`, que aplica automaticamente
  a cadeia de credenciais da AWS. Veja [DefaultCredentials.md](DefaultCrendentials.md)

**`getS3Client()`**

- Constrói e retorna uma instância do cliente S3 (`S3Client`), com as seguintes etapas:

| Método                             | Descrição                                                       |
|------------------------------------|-----------------------------------------------------------------|
| `S3Client.builder()`               | Inicia a construção do cliente.                                 |
| `region(Region.US_EAST_1)`         | Define a região da AWS (nesse caso, **US East – N. Virginia**). |
| `credentialsProvider(credentials)` | Configura o provedor de credenciais.                            |
| `build()`                          | Finaliza a construção e retorna o S3Client.                     |

### Operações com o Amazon S3 🛠️

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
