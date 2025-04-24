# 🛑 Desativar Proteções e Recursos do Windows - CapeV2

Este script `.bat` desativa diversas proteções nativas e recursos do Windows 10/11 para fins de análise ou configuração controlada em ambientes de laboratório (como o **CapeV2**).  
**Use com responsabilidade.**

---

## 📜 Instruções

1. **Antes de executar o script, desative manualmente a Proteção contra Adulteração (Tamper Protection)** — veja como no passo abaixo.
2. Crie um arquivo chamado `desativa-protecao.bat` — instruções de como fazer isso mais abaixo.
3. Copie o conteúdo do script para dentro do arquivo.
4. Execute o arquivo como **Administrador**.

---

## 🔒 Desativar Proteção Contra Adulteração (Tamper Protection)

A **Proteção contra adulteração** é um recurso de segurança do Windows 10 e 11 que impede alterações não autorizadas em configurações críticas, mesmo por usuários administradores.  
Ela protege itens como:
- Configurações do Windows Defender
- Proteção em tempo real
- Firewall
- Outras proteções do sistema

**Se essa proteção estiver ativa, o script abaixo não conseguirá desativar os recursos de segurança via Registro e serviços. Por isso, é necessário desativá-la manualmente antes.**

### 📌 Como desativar no Windows 10 e 11:
1. Abra a aplicação **Segurança do Windows**.
2. Clique em **Proteção contra vírus e ameaças**.
3. Em **Configurações de proteção contra vírus e ameaças**, clique em **Gerenciar configurações**.
4. Localize **Proteção contra adulteração** e desative.

Se estiver gerenciando via **Microsoft Defender for Endpoint** ou **Microsoft Intune**, ajuste a configuração nos painéis de administração antes de aplicar esse script.

---

## 📝 Como criar o arquivo `.bat` no Windows

1. **Abra o Bloco de Notas**
   - Pressione `Windows + R`
   - Digite `notepad` e pressione `Enter`

2. **Copie o conteúdo do script** (veja abaixo) e cole no Bloco de Notas.

3. **Salve o arquivo como `.bat`**
   - No Bloco de Notas, clique em `Arquivo` > `Salvar como...`
   - Em **Tipo**, escolha `Todos os arquivos (*.*)`
   - No **Nome do arquivo**, digite `desativa-protecao.bat`
   - Salve onde preferir (ex: Desktop)

4. **Execute o arquivo como Administrador**
   - Clique com o botão direito no arquivo `.bat`
   - Selecione **Executar como administrador**

---

## 📄 Script `desativa-protecao.bat`

```batch
@echo off
title Desativar Proteções e Recursos do Windows - CapeV2

echo.
echo === Desativando proteções e recursos do Windows ===
echo.

:: Desativa o Windows Defender
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableRealtimeMonitoring /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableBehaviorMonitoring /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableIOAVProtection /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableScriptScanning /t REG_DWORD /d 1 /f

:: Desativa a Telemetria
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 0 /f

:: Desativa Cortana
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v AllowCortana /t REG_DWORD /d 0 /f

:: Desativa a Localização
REG ADD "HKCU\Software\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\ConsentStore\location" /v Value /t REG_DWORD /d 0 /f

:: Desativa o OneDrive
REG ADD "HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v NoCloud /t REG_DWORD /d 1 /f
REG ADD "HKCU\Software\Policies\Microsoft\Windows\OneDrive" /v DisableFileSync /t REG_DWORD /d 1 /f

:: Desativa o UAC
REG ADD "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA /t REG_DWORD /d 0 /f

:: Desativa a Windows Store
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\WindowsStore" /v RemoveWindowsStore /t REG_DWORD /d 1 /f

:: Desativa as Atualizações Automáticas
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f

:: Desativa o Serviço de Pesquisa (Indexação)
sc config "WSearch" start= disabled
sc stop "WSearch"

:: Desativa todas as notificações de aplicativos e sistema
REG ADD "HKCU\Software\Microsoft\Windows\CurrentVersion\PushNotifications" /v ToastEnabled /t REG_DWORD /d 0 /f

:: Desativa sons de notificação
REG ADD "HKCU\AppEvents\Schemes\Apps\.Default" /ve /t REG_SZ /d "" /f

:: Desativa dicas, truques e sugestões do Windows
REG ADD "HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v SubscribedContent-338388Enabled /t REG_DWORD /d 0 /f
REG ADD "HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v SubscribedContent-338389Enabled /t REG_DWORD /d 0 /f

:: Desativa notificações de apps recém-instalados
REG ADD "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v Start_NotifyNewApps /t REG_DWORD /d 0 /f

echo.
echo Todas as configurações foram desativadas.
echo Reinicie a máquina para garantir que tudo foi aplicado.
pause
