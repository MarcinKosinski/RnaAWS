R na AWS
========

> 1 grudnia 2020 \[Marcin Kosiński\]
> (<a href="https://www.linkedin.com/in/mkosinski/" class="uri">https://www.linkedin.com/in/mkosinski/</a>)

Repozytorium udostępnione przez członka \[Fundacji Why R?\] (<a href="http://whyr.pl" class="uri">http://whyr.pl</a>)
---------------------------------------------------------------------------------------------------------------------

<img src = "https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/foundation.JPG" alt = "fundacja" />

Spis treści
===========

-   [AWS S3](#aws-s3)
-   [AWS RDS](#aws-rds)
-   [AWS EC2](#aws-ec2)

O AWS
=====

> Amazon Web Services (AWS) to najbardziej wszechstronna i szeroko
> stosowana platforma chmurowa na świecie, oferująca ponad 175 w pełni
> funkcjonalnych usług z centrów danych na całym świecie. Miliony
> klientów - w tym najszybciej rozwijające się start-upy, największe
> przedsiębiorstwa i czołowe agencje rządowe - korzystają z AWS w celu
> obniżenia kosztów, zwiększenia elastyczności i szybszego wprowadzania
> innowacji.

Logowanie
=========

-   <a href="https://aws.amazon.com/" class="uri">https://aws.amazon.com/</a>
    -\> Utwórz konto AWS
-   Konsola
    <a href="https://YOUR_IAM.signin.aws.amazon.com/console" class="uri">https://YOUR_IAM.signin.aws.amazon.com/console</a>
    -   Przykład
        <a href="https://021312779203.signin.aws.amazon.com/console" class="uri">https://021312779203.signin.aws.amazon.com/console</a>

<img src = "https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/logging.JPG" alt = "fundacja" />

Konsola zarządzania
===================

-   <a href="https://console.aws.amazon.com/" class="uri">https://console.aws.amazon.com/</a>

<img src = "https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/console.JPG" alt = "console" />

Usługi, które omówimy
=====================

<table>
<colgroup>
<col style="width: 66%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th>Serwis</th>
<th>URL</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>AWS Simple Cloud Storage (S3)</td>
<td><a href="aws.amazon.com/s3/">aws.amazon.com/s3/</a></td>
</tr>
<tr class="even">
<td>Amazon RDS - relacyjna baza danych w chmurze</td>
<td><a href="aws.amazon.com/rds/">aws.amazon.com/rds/</a></td>
</tr>
<tr class="odd">
<td>AWS Elastic Computing (EC2) - bezpłatne usługi w chmurze z AWS</td>
<td><a href="aws.amazon.com/ec2/">aws.amazon.com/ec2/</a></td>
</tr>
<tr class="even">
<td>Amazon IAM - zarządzanie tożsamością i dostępem</td>
<td><a href="aws.amazon.com/iam/">aws.amazon.com/iam/</a></td>
</tr>
</tbody>
</table>

AWS S3
======

Amazon Simple Storage Service (Amazon S3) to usługa obiektowej pamięci
masowej, która oferuje wiodącą w branży skalowalność, dostępność danych,
bezpieczeństwo i wydajność. Oznacza to, że klienci każdej wielkości i z
każdej branży mogą go używać do przechowywania i ochrony dowolnej ilości
danych w różnych przypadkach użycia, takich jak jeziora danych, witryny
internetowe, aplikacje mobilne, tworzenie kopii zapasowych i
przywracanie, archiwizacja, aplikacje korporacyjne, urządzenia IoT i
duże Analityka danych.

### Potrzebne dane logowania

Przejdź do swojego profilu -\> Moje poświadczenia bezpieczeństwa -\>
Utwórz klucz dostępu

<img src = "https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/credentials.png" alt = "credentials" />
<img src = "https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/access_key.png" alt = "access_key" />

Jest to również miejsce, w którym można dodać MFA (uwierzytelnianie
wieloskładnikowe).

<img src = "https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/mfa.JPG" alt = "access_key" />

Przechowuj poświadczenia w swoim pliku `.Renviron` w katalogu roboczym

``` r
AWS_ACCESS_KEY_ID = your_access_key
AWS_SECRET_ACCESS_KEY = your_secret_access_key
AWS_DEFAULT_REGION = eu-central-1
```

Pamiętaj, aby określić `AWS_DEFAULT_REGION` w regionie, w którym jesteś
zarejestrowany.

Teraz możesz załadować poświadczenia za pomocą

``` r
readRenviron('.Renviron')
```

Następnym razem ten plik zostanie odczytany podczas uruchamiania sesji
języka R.

### Praca z S3

``` r
# install.packages(c('aws.s3', 'readr'))
library(aws.s3)
library(readr)
```

Przechowywanie danych

``` r
iris_small <- iris[1:50, ]

aws.s3::s3write_using(
  x = iris_small,
  FUN = write_csv,
  object = 'iris_small.csv',
  bucket = 'wdi-example'
)

aws.s3::s3saveRDS(
  x = iris,
  object = 'iris.rds',
  bucket = 'wdi-example'
)
```

Czytanie danych

``` r
iris_small_from_s3_csv <-
  aws.s3::s3read_using(
    FUN = read_csv,
    object = 'iris_small.csv',
    bucket = 'wdi-example'
  )

iris_small_from_s3 <-
  aws.s3::s3readRDS(
    object = 'iris.rds',
    bucket = 'wdi-example'
  )
```

### Tworzenie bucket’u

``` r
aws.s3::putbucket('example-website-wdi')
aws.s3::putbucket('example-website-wdi-2')
```

### S3 bucket jako strona

Po utworzeniu zasobnika możesz przesłać plik .html

``` r
aws.s3::put_object(
  file = 'html/index.html',
  object = 'index.html', 
  bucket = 'example-website-wdi-2', 
  headers = list(`Content-Type` = 'text/html')
)
```

i ręcznie przypisz policy do bucketu

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/bucket_policy.JPG" alt="access_key" />

``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::example-website-wdi/*"
        }
    ]
}
```

albo automatycznie

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/bucket_policy_automated.JPG" alt="access_key" />

``` r
aws.s3::put_bucket_policy(
  bucket = 'example-website-wdi-2',
  policy = 'json/bucket_policy.json'
)
```

#### Włącz hosting statycznej witryny internetowej

W opcjach bucket’u, wciśnij properties

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/properties.JPG" alt="properties" />

i przejdź do statycznego hostingu witryn internetowych

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/static_website_hosting.JPG" alt="static_website_hosting" />

Włącz opcję i podaj nazwę pliku index.html, który oznacza stronę
docelową Twojej witryny

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/index.JPG" alt="index" />

Po tym powinieneś być w stanie zobaczyć adres swojej witryny

<http://example-website-whyr.s3-website.eu-central-1.amazonaws.com/>

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/website.JPG" alt="website" />

AWS RDS
=======

Usługa Amazon Relational Database Service (Amazon RDS) ułatwia
konfigurację, obsługę i skalowanie relacyjnej bazy danych w chmurze.
Zapewnia ekonomiczną pojemność o zmiennym rozmiarze, a jednocześnie
automatyzuje czasochłonne zadania administracyjne, takie jak
udostępnianie sprzętu, konfiguracja bazy danych, łatanie i tworzenie
kopii zapasowych. Pozwala skupić się na aplikacjach, dzięki czemu możesz
zapewnić im szybką wydajność, wysoką dostępność, bezpieczeństwo i
kompatybilność, których potrzebują.

### Praca z AWS RDS

``` r
#install.packages(c('DBI', 'RPostgres', 'dplyr'))
library(DBI)
library(RPostgres)
library(dplyr)
```

Z panelu konsoli RDS możesz stworzyć bazę danych
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/create_db.JPG" alt="create" />

i wybrac żądany silnik.
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/engine_db.JPG" alt="engine" />

Powinieneś być w stanie utworzyć poświadczenia dla przyszłego dostępu.
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/credentials_db.JPG" alt="credentials" />

Wybierz rozmiar, którego potrzebujesz
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/size_db.JPG" alt="size" />

i pamiętaj, aby nazwać bazę danych na hoście
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/name_db.JPG" alt="name" />

Po utworzeniu bazy danych powinno być możliwe uzyskanie jej endpoint’u
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/endpoint_db.JPG" alt="name" />

``` r
rds_credentials <- list(
  user = "marcin",
  password = "staycalm",
  port = "5432",
  host = "example.cnmgyyxquanl.eu-central-1.rds.amazonaws.com",
  dbname = "example"
)
```

Musisz pamiętać o ustawieniach grup bezpieczeństwa
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/security_group_name.JPG" alt="security_group_name" />

Możesz edytować grupę zabezpieczeń
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/edit_security.png" alt="edit_security" />

Aby port 5432 był dostępny dla ruchu
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/rules_db.JPG" alt="rules_db" />

Upewnij się, że baza danych jest publicznie dostępna

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/accessibility.JPG" alt="accessibility" />

Możesz to zmienić w ustawieniach łączności z bazą danych.
<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/connectivity.JPG" alt="connectivity" />

``` r
# connect to the database
con <- DBI::dbConnect(
  RPostgres::Postgres(),
  dbname = rds_credentials$dbname,
  host   = rds_credentials$host,
  port     = rds_credentials$port,
  user     = rds_credentials$user,
  password = rds_credentials$password
)
```

Po nawiązaniu połączenia możesz wyświetlić listę aktualnych tabel i
utworzyć nowe

``` r
DBI::dbListTables(con)
dplyr::copy_to(
  dest = con, 
  name = 'cars', 
  df = cars,          
  temporary = FALSE
)
DBI::dbListTables(con)
DBI::dbListFields(con, 'iris')
```

``` r
iris_db <-
  tbl(con, 'iris') %>%
  filter(Species == 'setosa') %>%
  collect()
```

``` r
DBI::dbSendQuery(
  con,
  "CREATE TABLE example_table_2(
    id INT PRIMARY KEY NOT NULL,
    task INT NOT NULL,
    optio INT NOT NULL
  );")

library(RPostgres)
# db_insert_into(
#   con,
#   table = 'example_table',
#   values =
#     tibble(
#       id = 1:3,
#       task = 10:12,
#       optio = 100:102
#     )
# )
```

AWS EC2
=======

Amazon Elastic Compute Cloud (Amazon EC2) to usługa internetowa
zapewniająca bezpieczną pojemność obliczeniową o zmiennym rozmiarze w
chmurze. Został zaprojektowany, aby ułatwić programistom przetwarzanie w
chmurze w skali internetowej. Prosty interfejs usług sieciowych Amazon
EC2 umożliwia uzyskiwanie i konfigurowanie wydajności przy minimalnych
tarciach. Zapewnia pełną kontrolę nad zasobami komputerowymi i umożliwia
pracę w sprawdzonym środowisku obliczeniowym firmy Amazon.

### Praca z AWS EC2

``` r
install.packages("aws.ec2", repos = c(getOption("repos"), "http://cloudyr.github.io/drat"))
library(aws.ec2)
```

RStudio Server Amazon Machine Image (AMI) -
<a href="https://www.louisaslett.com/RStudio_AMI/" class="uri">https://www.louisaslett.com/RStudio_AMI/</a>

<img src="https://raw.githubusercontent.com/MarcinKosinski/RnaAWS/main/img/important.JPG" alt="important" />
