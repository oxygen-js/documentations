# Использование Linux Perf

[Linux Perf](https://perf.wiki.kernel.org/index.php/Main_Page) предоставляет низкоуровневое профилирование CPU с кадрами JavaScript, нативными и на уровне ОС.

**Важно**: это руководство доступно только для Linux.

## Как это сделать

Linux Perf обычно доступен через пакет `linux-tools-common`. С помощью `--perf-basic-prof` или `--perf-basic-prof-only-functions` мы можем запустить приложение Node.js с поддержкой _perf_events_.

`--perf-basic-prof` всегда пишет в файл (/tmp/perf-PID.map), что может привести к бесконечному росту занятого места на диске. Если это вас беспокоит, используйте либо модуль [linux-perf](https://www.npmjs.com/package/linux-perf), либо `--perf-basic-prof-only-functions`.

Основное различие между ними в том, что `--perf-basic-prof-only-functions` даёт меньше вывода — это приемлемый вариант для профилирования в продакшене.
```
# Запускаем приложение и получаем PID
$ node --perf-basic-prof-only-functions index.js &
[1] 3870
```

Сеанс командной оболочки

Затем записывайте события с нужной частотой:
```
$ sudo perf record -F 99 -p 3870 -g
```

На этом этапе вы, возможно, захотите провести нагрузочное тестирование приложения, чтобы сгенерировать больше записей для надёжного анализа. Когда работа сделана, завершите процесс perf, отправив команде сигнал SIGINT (Ctrl-C).

`perf` создаст файл внутри папки `/tmp`, обычно называемый `/tmp/perf-PID.map` (в примере выше: `/tmp/perf-3870.map`), содержащий трассировки для каждой вызванной функции.

Чтобы агрегировать эти результаты в отдельный файл, выполните:
```
$ sudo perf script > perfs.out
```

Сырой вывод может быть немного труден для понимания, поэтому обычно сырой файл используют для генерации флейм-графов (flame graph) ради лучшей визуализации.

![Пример флейм-графа nodejs](https://user-images.githubusercontent.com/26234614/129488674-8fc80fd5-549e-4a80-8ce2-2ba6be20f8e8.png)

Чтобы сгенерировать флейм-граф из этого результата, следуйте [этому руководству](/learn/diagnostics/flame-graphs#create-a-flame-graph-with-system-perf-tools), начиная с шага 6.

Поскольку `perf` не является инструментом, специфичным для Node.js, у него могут возникать сложности с тем, как код JavaScript оптимизируется в Node.js. Дополнительные сведения см. в разделе [проблемы вывода perf](/learn/diagnostics/flame-graphs#perf-output-issues).

## Полезные ссылки

  * [Флейм-графы](/learn/diagnostics/flame-graphs)
  * <https://www.brendangregg.com/blog/2014-09-17/node-flame-graphs-on-linux.html>
  * <https://perf.wiki.kernel.org/index.php/Main_Page>
  * <https://blog.rafaelgss.com.br/node-cpu-profiler>

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/diagnostics/poor-performance/using-linux-perf. Оригинал распространяется по лицензии MIT._
