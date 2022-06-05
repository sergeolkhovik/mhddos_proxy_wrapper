# mhddos_proxy_wrapper

## Скрипт:

Скрипт на bash для автоматичної роботи mhddos_proxy + ansible playbook для автоматичного розгортання на будь-якому VPS (наприклад DO):
- запускає mhddos_proxy в фоні та слідкує за допомогою vnstat, щоб не вижерти весь трафік (самий дешевий інстанс на DO має 1ТБ на місяць всього)
- автоматично шейпить трафик, щоб вистачило на місяць
- раз в годину завантажує файл з цілями, якщо відмінний від поточного - перезапускає mhddos_proxy з новим файлом (змінна ddos_sleep в playbook, SLP в скрипті або опція -s)

Для роботи потрібно, щоб локальний користувач мав безпарольний sudo - для шейпингу (за допомогою wondershaper).
Також шейпінг можна вимкнути за допомогою опції -n.

Отже можна раз запустити (або через сервіс) і забути, плюс розгорнути за допомогою ansible (тоді сервіс створиться і запуститься автоматично).

mhddos_proxy встановлюєте як зазвичай, шлях до нього прописуєете в SRC.

## Потрібні пакети:

- vnstat
- coreutils (numfmt)
- mailutils
- wondershaper
- wget
- diffutils

## Наприклад Digital Ocean:
- створюємо Ubuntu 22.04 droplet, прописуємо ssh ключ
- логинимось на новий хост (ssh root@IP)
- створюємо локального користувача (adduser serge)
- перекидаємо ключи (install -d -o serge -g serge -m 700 ~serge/.ssh; install -o serge -g serge -m 600 /root/.ssh/authorized_keys ~serge/.ssh)
- блокуємо PermitRootLogin в /etc/ssh/sshd_config
- додаємо безпарольний sudo (EDITOR=vim visudo /etc/sudoers.d/serge; serge ALL=(ALL) NOPASSWD: ALL)
- додаємо цей сервер в hosts
- запускаєм ansible (ansible-playbook deploy.yaml)
- voi-la
