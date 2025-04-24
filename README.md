# 🛑 Desativar Proteções do Windows — CapeV2

Este script `.bat` desativa diversas proteções nativas do Windows para fins de análise ou configuração controlada em ambientes de laboratório (como o CapeV2). **Use com responsabilidade**.

---

## 📜 Instruções

1. Crie um arquivo chamado `desativa-protecao.bat`
2. Copie o conteúdo abaixo e cole dentro do arquivo.
3. Execute como **Administrador**.

---

## 📄 Script `desativa-protecao.bat`

```batch
@echo off
title Desativar Proteções do Windows - CapeV2

echo.
echo === Desativando proteções do Windows ===
echo.

:: Desativa Windows Defender via Registro
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableRealtimeMonitoring /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableBehaviorMonitoring /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableIOAVProtection /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableScriptScanning /t REG_DWORD /d 1 /f

:: Desativa MAPS e envio de amostras para a Microsoft
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v SpynetReporting /t REG_DWORD /d 0 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v SubmitSamplesConsent /t REG_DWORD /d 2 /f

:: Desativa notificações e ícone da Central de Segurança
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender Security Center" /v DisableNotifications /t REG_DWORD /d 1 /f
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender Security Center\Systray" /v HideSystray /t REG_DWORD /d 1 /f

:: Desativa o SmartScreen
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v EnableSmartScreen /t REG_DWORD /d 0 /f

:: Desativa o Firewall para todos os perfis
netsh advfirewall set allprofiles state off

:: Desativa o Windows Update
sc stop wuauserv
sc config wuauserv start= disabled

echo.
echo Todas as proteções foram desativadas.
echo Reinicie a máquina para garantir que tudo foi aplicado.
pause
