# Модуль 8. Домашнее задание

## Bank Application

### Рефакторинг

Следующим этапом рефакторинга мы окончательно отделим классы хранящие данные от классов с логикой.

Наши пакеты должны иметь следующий вид

    ua.spalah.bank ┓
                   ┣ models
                   ┣ services ┓
                   ┃          ┗ impl
                   ┗ listeners

Классы банк, клиент и счета перемещаются в пакет models.
Интерфейсы сервисов - в services, их реализации в services.impl

В классах моделях оставляем только поля и геттеры и сеттеры для них. В банке - список клиентов.
В клиенте - имя, пол, список счетов и активный счет.
Классы счетов поменяются больше всего. Во-первых, создаем интерфейс Account

```java
public interface Account {
    AccountType getType();
    
    double getBalance();
    void setBalance(double balance);
}
```
Создаем Enum, где будем хранить типы аккаунтов
```java
public enum AccountType {
    SAVING, CHECKING
}
```
Из классов счетов остается SavingAccount c типом и балансом. CheckingAccount наследует от него и добавляет овердрафт.
В этих классах остаются только геттеры и сеттеры, без логики. В классе клиента храним счета в полях с типом интерфейса.

В дополнение к нашим двум серфисам, создаем BankService и BankServiceImpl.
Все логика (методы с логикой) из классов моделей мигрирует в соответствующий класс сервиса,
который в свою очередь манипулирует моделями только геттерами и сеттерами (deposit и withdraw в AccountService, findClientByName в BankService и т.д). 

### Добавление исключений

Теперь в случае ошибочных ситуаций мы будем не просто писать в консоль об ошибке, а будем кидаться ексепшенами :)

Помимо стандартного IllegalArgumentException, вам понадобятся свои классы исключений.

Создаем общий класс исключения для наших потребностей, остальные будем наследовать от него
```java
public class BankException extends Exception
```
И вам понадобится еще три типа исключений
```java
public class ClientNotFoundException extends BankException
public class NotEnoughFundsException extends BankException
public class OverdraftLimitExceededException extends NotEnoughFundsException
```
Названия говорят сами за себя.
Первое кидаем, если клиент не найден.
Второе, если не хватает денег на счету.
А третье если в CheckingAccount не хватает денег для снятия даже с учетом овердрафта.

Создаем им конструкторы, чтобы можно было максимально просто ими пользоваться.
Посмотрите пример в коде с урока.