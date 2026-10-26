# ДЗ 1. Изучение средств защиты ОС GNU/Linux

Стенд: Fedora Server 44 в VirtualBox, имя хоста `m265280` (номер зачетки). Вся работа выполнена из командной строки по SSH, полный журнал команд в `history.out`.

| Раздел | Как выполнено | Где видно |
|---|---|---|
| 1. Пользователь | `groupadd students`, `useradd -u 1234 -g students -m user1`, `passwd user1`, `chage -M 90 user1` (пароль меняется каждые 90 дней) | `passwd`, `group`, `shadow`, `stat.out` |
| 2.1. Файлы с set-UID | `find / -type f -perm -4000` | `suid_files.out` |
| 2.2. Процессы с EUID 0 и RUID обычного пользователя | в первом терминале ничего не нашлось, во втором запущен `passwd`, поиск `ps -eo pid,ruid,euid,ruser,euser,comm` с фильтром `ruid >= 1000 && euid == 0` | `suid_processes.out` |
| 3. set-UID | копия `/usr/bin/cat` в `/home/user1/cat_suid`, владелец root, `chmod u+s`; `user1` через копию читает `/etc/shadow`, обычным `cat` получает отказ | `stat.out` (права 4755) |
| 4. Capabilities | копия `/usr/bin/chown` в `/home/user1/chown_cap`, `setcap cap_chown+ep`; `user1` через копию меняет владельца файла, обычным `chown` получает отказ | `getcap.out`, `stat.out` |
| 5. sudo на установку времени | в `/etc/sudoers` через `visudo` строка `user1 ALL=(root) /usr/bin/date, /usr/bin/timedatectl set-time *`; синхронизация NTP отключена (`timedatectl set-ntp false`), иначе `set-time` отклоняется; смена часового пояса для `user1` запрещена | `sudoers` |

После перезагрузки проверено, что пользователь, срок пароля, бит set-UID, capabilities, правило sudo и отключенный NTP сохранились (видно на `mephi-screenshot.png`).

Строка 75 в `history.out` имеет время 12:30 вне общего порядка. Это след проверки раздела 5, когда `user1` переставлял системные часы.

Пароли на стенде одноразовые и нигде больше не используются.
