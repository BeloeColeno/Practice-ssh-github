# Практическая работа: SSH-доступ к GitHub

В этой работе применяется та же схема, как в предидущей, но сервером является GitHub:

1. локальный SSH-клиент и ключевая пара;
2. добавление публичного ключа на сервер (в аккаунт GitHub);
3. проверка SSH-подключения;
4. передача изменений по SSH через Git (`clone` и `push`).

## 1. SSH-клиент и ключевая пара

В Windows были проверены:

```text
OpenSSH_for_Windows_9.5p2, LibreSSL 3.8.2
git version 2.56.0.windows.4
```

Ключ создан в PowerShell командой:

```powershell
ssh-keygen -t ed25519 -C "github-ssh-practice" -f "$HOME\.ssh\id_ed25519"
```

Приватный ключ защищён passphrase и не добавляется в репозиторий. Публичный
ключ находится в `$HOME\.ssh\id_ed25519.pub`.

Проверка fingerprint публичного ключа:

```text
SHA256:kok0faJXY02wgyFI1Bqb3qQAFumcOPWktaUzHfoMNs8 github-ssh-practice (ED25519)
```

Для использования ключа без повторного ввода passphrase был запущен Windows
OpenSSH Authentication Agent, после чего выполнено:

```powershell
ssh-add "$HOME\.ssh\id_ed25519"
```

## 2. Добавление ключа в GitHub

Публичный ключ скопирован командой:

```powershell
Get-Content "$HOME\.ssh\id_ed25519.pub" | Set-Clipboard
```

Затем он добавлен в **GitHub → Settings → SSH and GPG keys → New SSH key**.

Подтверждение GitHub:

```text
You have successfully added the key 'Windows GitHub SSH practice'.
Authentication key: Windows GitHub SSH practice
Fingerprint: SHA256:kok0faJXY02wgyFI1Bqb3qQAFumcOPWktaUzHfoMNs8
```

## 3. Проверка SSH-подключения

Команда:

```powershell
ssh -T git@github.com
```

Результат:

```text
Hi BeloeColeno! You've successfully authenticated, but GitHub does not provide shell access.
```

Сообщение означает, что GitHub узнал пользователя по SSH-ключу. Код возврата
SSH-команды равен `1`, потому что GitHub намеренно не предоставляет shell-доступ.

## 4. Работа с репозиторием по SSH

Репозиторий был клонирован по SSH-URL:

```powershell
git clone git@github.com:BeloeColeno/practice-ssh-github.git
```

Для Git настроен системный OpenSSH, связанный с Windows SSH-agent:

```powershell
git config core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"
```

Далее выполнены команды:

```powershell
git add README.md
git commit -m "Document GitHub SSH access"
git push -u origin main
```

`origin` использует SSH-адрес:

```text
git@github.com:BeloeColeno/practice-ssh-github.git
```

Ключ был загружен в SSH-agent заранее, поэтому при `clone` и `push` пароль
GitHub не вводился.
