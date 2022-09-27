# Домашнее задание к занятию "7.6. Написание собственных провайдеров для Terraform."

Бывает, что 
* общедоступная документация по терраформ ресурсам не всегда достоверна,
* в документации не хватает каких-нибудь правил валидации или неточно описаны параметры,
* понадобиться использовать провайдер без официальной документации,
* может возникнуть необходимость написать свой провайдер для системы используемой в ваших проектах.   

## Задача 1. 
Давайте потренируемся читать исходный код AWS провайдера, который можно склонировать отсюда: 
[https://github.com/hashicorp/terraform-provider-aws.git](https://github.com/hashicorp/terraform-provider-aws.git).
Просто найдите нужные ресурсы в исходном коде и ответы на вопросы станут понятны.  


1. Найдите, где перечислены все доступные `resource` и `data_source`, приложите ссылку на эти строки в коде на 
гитхабе.  
```doctest
resource = https://github.com/hashicorp/terraform-provider-aws/blob/8e4d8a3f3f781b83f96217c2275f541c893fec5a/aws/provider.go#L411
data_source = https://github.com/hashicorp/terraform-provider-aws/blob/8e4d8a3f3f781b83f96217c2275f541c893fec5a/aws/provider.go#L169

```
2. Для создания очереди сообщений SQS используется ресурс `aws_sqs_queue` у которого есть параметр `name`. 

   * С каким другим параметром конфликтует `name`? Приложите строчку кода, в которой это указано.
```doctest
ConflictsWith: []string{"name_prefix"}
https://github.com/hashicorp/terraform-provider-aws/blob/6076d5a60ec814b243bc45170d67cb268a39d927/internal/service/sqs/queue.go#L97
```
   * Какая максимальная длина имени?
```doctest
Длина не должна превышать 80 символов. При этом, если очередь относится к `.fifo`, то 75 (без учета суффикса)
https://github.com/hashicorp/terraform-provider-aws/blob/b8b7fee0e5cf4b469d8d4d2a2dc01ed13654a03a/aws/validators.go#L1035
[name_fifo] https://github.com/hashicorp/terraform-provider-aws/blob/6076d5a60ec814b243bc45170d67cb268a39d927/internal/service/sqs/queue.go#L411
```
   * Какому регулярному выражению должно подчиняться имя? 
```doctest
* В ветке `main`:
  * https://github.com/hashicorp/terraform-provider-aws/blob/6076d5a60ec814b243bc45170d67cb268a39d927/internal/service/sqs/queue.go#L413
  * `^[a-zA-Z0-9_-]{1,80}$`
  * Здесь учтена сразу длина наименования. 
  * Имена очередей должны состоять только из прописных и строчных букв, цифр, знаков подчеркивания и дефисов, и должны иметь длину от 1 до 80 символов.
* В ветке `master`:
  * https://github.com/hashicorp/terraform-provider-aws/blob/b8b7fee0e5cf4b469d8d4d2a2dc01ed13654a03a/aws/validators.go#L1041
  * `^[0-9A-Za-z-_]+(\.fifo)?$`
  * Имена очередей должны состоять только из прописных и строчных букв, цифр, знаков подчеркивания и дефисов. Для очереди FIFO (first-in-first-out) имя должно заканчиваться суффиксом `.fifo`.
```