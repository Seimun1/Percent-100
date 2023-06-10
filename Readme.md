# «Выстраивание процесса непрерывной интеграции»

Цель задания:

- Научиться настраивать CI на основе GitHub Actions.

- Инструкция к заданию:
1. Скачайте и установите профессиональный редактор кода Intellij Idea Community Version.
2. Откройте IDEA и создайте и настройте новый Maven-проект. Под каждую задачу следует создавать отдельный проект, если обратное не сказано в условии.
3. Создайте пустой репозиторий на GitHub и свяжите его с папкой вашего проекта, а не с какой-либо другой.
4. Правильно настройте репозиторий в плане .gitignore. Проигнорируйте папки .idea и target (раньше была out) и .iml-файл — их в репозитории быть не должно.
5. Закоммитьте и запушьте созданный проект на ГитХаб, настройте GitHub Actions, сделайте git pull.
6. Выполните в IDEA требуемую задачу согласно условию.
7. Проверьте соблюдение правил форматирования кода.
8. Убедитесь что при запуске mvn clean verify (раньше было mvn clean test) все тесты запускаются, проходят, а сборка завершается успешно
9. Закоммитьте и отправьте в репозиторий содержимое папки проекта.
10. Убедитесь, что CI запустился на последнем коммите и завершился успешно — появилась зелёная галочка.

Задание 1 — обязательное:

Перед вами код сервисного класса:

    package ru.netology.statistic;

    public class StatisticsService
      public long findMax(long[] incomes) {
      long currentMax = incomes[0];
      for (long income : incomes) {
        if (currentMax < income) {
        currentMax = income;
        }
      }
      return currentMax;
     }
    }
И код тест-класса, который его тестирует:

    package ru.netology.statistic;

    import org.junit.jupiter.api.Test;
    import org.junit.jupiter.api.Assertions;

    public class StatisticsServiceTest {

    @Test
    void findMax() {
    StatisticsService service = new StatisticsService();

    long[] incomesInBillions = {12, 5, 8, 4, 5, 3, 8, 6, 11, 11, 12};
    long expected = 12;

    long actual = service.findMax(incomesInBillions);

    Assertions.assertEquals(expected, actual);
      }
    }
Ваша задача состоит в том, чтобы:

1. создать Maven-проект и поместить в него эти два класса;
2. запустить mvn clean test и убедиться, что тесты проходят;
3. создать публичный репозиторий и запушить в него проект;
4. настроить CI на основе GitHub Actions, после чего не забыть сделать git pull;
5. добавить в проект JaCoCo и настроить его в режиме обрушения сборки по недостаточному покрытию, а именно 100% покрытие по счётчику BRANCH;
6. запустить mvn clean verify и убедиться, что сборка упадёт из-за недостаточного покрытия;
7. проанализировать сгенерированный отчёт по покрытию, дописать недостающие тесты для полного покрытия, сам сервисный класс трогать нельзя;
8. сделать коммит и пуш, убедиться, что сборка на ГитХабе проходит.

Настройка CI на основе Github Actions:

CI

- После связывания локального репозитория с удалённым и первого пуша в заготовки проекта, время настроить CI на основе GitHub Actions.
- Шаблон вашего maven.yml должен выглядеть вот так, убедитесь, что всё совпадает с вашим шаблоном, например, что вы указали фазу verify, а не package:
  
name: Java CI with Maven

on: [push, pull_request]

jobs:
build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
    - name: Build with Maven
      run: mvn -B -e verify

**JaCoCo**

            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>0.8.5</version>
                ...

Инициализация:

                    <execution>
                        <id>prepare-agent</id>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>

В режиме генерации отчётов:

                    <execution>
                        <id>report</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>

В режиме проверки и обрушения сборки по уровню покрытия по счётчику BRANCH:

                    <execution>
                        <id>check</id>
                        <goals>
                            <goal>check</goal>
                        </goals>
                        <configuration>
                            <rules>
                                <rule>
                                    <limits>
                                        <limit>
                                            <counter>BRANCH</counter>
                                            <value>COVEREDRATIO</value>
                                            <minimum>100%</minimum>
                                        </limit>
                                    </limits>
                                </rule>
                            </rules>
                        </configuration>
                    </execution>

Не забываем про pull:

Т.к. настройка CI через Github Actions технически реализуется через автосоздание коммитов в удалённом репозитории, во избежание конфликтов, после настройки CI сразу сделайте git pull в локальном репозитории.

