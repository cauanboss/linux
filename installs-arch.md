# Instalações — Arch Linux (CachyOS)

Ordem recomendada para instalação do zero.

---

## ⚠️ Antes de começar: Corrigir keyring do CachyOS

Se ao rodar `pacman -Sy` aparecer o erro:
```
erro: cachyos-extra-v3: a assinatura de "CachyOS <admin@cachyos.org>" é inválida
```

Resolva com:
```bash
sudo pacman-key --refresh-keys
sudo pacman -Syy
```

Se persistir, forçar reinstalação do keyring:
```bash
sudo rm -f /var/lib/pacman/sync/cachyos-extra-v3.db*
curl -O https://mirror.cachyos.org/repo/x86_64/cachyos/cachyos-keyring-20240331-1-any.pkg.tar.zst
sudo pacman -U --overwrite='*' cachyos-keyring-20240331-1-any.pkg.tar.zst
sudo pacman -Syy
```


---

## 1. Base

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 1.1 | **paru** | `sudo pacman -S paru` | AUR helper (tudo que depende do AUR precisa disso) |
| 1.2 | **unzip** | `sudo pacman -S unzip` | Descompactar ZIP |
| 1.3 | **openssh** | `sudo pacman -S openssh` | SSH |

## 2. Dev Core

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 2.1 | **n** | `sudo pacman -S n` | Gerenciador de versões Node.js |
| 2.2 | **kilo** | `npm i -g kilo` | Gerenciador de projetos com IA |

## 3. Shell e Terminal

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 3.1 | **zsh** | `sudo pacman -S zsh` | Shell alternativo |
| 3.2 | ohmyzsh | `sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` | Framework de config do zsh |
| 3.3 | zsh-autosuggestions | `git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions` | Sugestões de comandos |
| 3.4 | zsh-syntax-highlighting | `git clone https://github.com/zsh-users/zsh-syntax-highlighting ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting` | Highlight de sintaxe |
| 3.5 | **kitty** | `sudo pacman -S kitty` | Terminal GPU (padrão no Hyprland). SSH: usar `kitten ssh` (não `ssh` puro) |

## 4. Ferramentas Dev

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 4.1 | **docker** | `sudo pacman -S docker` | Containers |
| 4.2 | **ripgrep** | `sudo pacman -S ripgrep` | Busca rápida em código |
| 4.3 | **rtk** | `npm i -g rtk` | Proxy que reduz tokens LLM |
| 4.4 | **micro** | `sudo pacman -S micro` | Editor de texto no terminal |
| 4.5 | **meld** | `sudo pacman -S meld` | Diff/merge visual |
| 4.6 | **btop** | `sudo pacman -S btop` | Monitor de sistema no terminal |
| 4.7 | **fastfetch** | `sudo pacman -S fastfetch` | Info do sistema |

## 5. Navegadores e Editor

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 5.1 | **firefox** | `sudo pacman -S firefox` | Navegador |
| 5.2 | **brave** | `paru -S brave-bin` | Navegador alternativo |
| 5.3 | cursor | AppImage / site | Editor com IA |

## 6. Cloud e Sincronização

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 6.1 | **rclone** | `sudo pacman -S rclone` | Sync de cloud |
| 6.2 | **rclone-ui-bin** | `paru -S rclone-ui-bin` | Interface gráfica pro rclone |
| 6.3 | ssh-agent | config manual → `installs-arch.md` original | Agente SSH único pra sessão |

## 7. Bancos de Dados

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 7.1 | **datagrip** | JetBrains Toolbox | IDE de banco de dados |

## 8. Gaming

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 8.1 | **steam** | `sudo pacman -S steam` | Plataforma de jogos |
| 8.2 | **gamemode** | `sudo pacman -S gamemode` | Otimização de CPU/GPU em jogos |

## 9. Utilitários

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 9.1 | **wl-clipboard** | `sudo pacman -S wl-clipboard` | Clipboard Wayland |
| 9.2 | **dolphin** | `sudo pacman -S dolphin` | Gerenciador de arquivos |
| 9.3 | **shelly** | `sudo pacman -S shelly` | Gerenciador gráfico de pacotes |
| 9.4 | **gear-lever** | flatpak | Gerenciador de AppImages |
| 9.5 | **warehouse** | flatpak | Gerenciador de Flatpaks |
| 9.6 | **mission center** | flatpak | Monitor de sistema gráfico |
| 9.7 | **flatsweep** | flatpak | Limpeza de resíduos Flatpak |
| 9.8 | **ente auth** | flatpak | Autenticador 2FA |
| 9.9 | **localsend** | flatpak | Compartilhamento de arquivos local |
| 9.10 | **transmission** | `sudo pacman -S transmission` | Cliente BitTorrent |
| 9.11 | **linux toys** | `curl -fsSL linux.toys/install.sh \| sh` | Utilitários Linux |

## 10. Segurança e Sistema

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 10.1 | **gufw** | `sudo pacman -S gufw` | Firewall gráfico |
| 10.2 | **clamav** | `sudo pacman -S clamav` | Antivírus |
| 10.3 | **hypridle** | `sudo pacman -S hypridle` | Gerenciador de inatividade |

## 11. Aparência

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 11.1 | **sddm-astronaut-theme** | `paru -S sddm-astronaut-theme` | Tema de login SDDM |

---

## 12. Ajustes Pós-Instalação

Depois de tudo instalado, aplicar estas configurações:

| Ordem | Ajuste | O que faz |
|---|---|---|
| 12.1 | **Monitor principal + 155Hz + VRR** | Configurar monitor externo como principal, resolução/refresh nativa, e FreeSync |
| 12.2 | Hypridle config | `hypridle.conf` — timeout 5min lock, 10min dpms-off |
| 12.3 | Noctalia corner migration | `config.toml` — `corner = { ... }` em vez de `radius_*` |
| 12.4 | Noctalia mapeado | Launcher (`SUPER+Space`), lock (`SUPER+L`), notifs (`SUPER+A`), screenshots (`Print`) |
| 12.5 | Kitty SSH | Usar `kitten ssh` em vez de `ssh` puro |

### 12.1 — Monitor Principal + 155Hz + VRR

**Problema:** O monitor externo não era reconhecido como principal (workspaces abriam no laptop), estava a 60Hz mesmo com painel 155Hz, e FreeSync não funcionava.

**Solução:** Requer 3 arquivos de config do Hyprland (CachyOS):

#### Passo 1: Descobrir nome da saída do monitor
```bash
hyprctl monitors | grep Monitor
# Exemplo de saída:
# Monitor HDMI-A-1 (ID ...):  
# Monitor eDP-1 (ID ...):
```

#### Passo 2: Configurar `~/.config/hypr/config/variables.lua`
```lua
-- Monitors
MONITOR1 = "HDMI-A-1"   -- monitor externo (ou DP-1, dependendo do cabo)
MONITOR2 = "eDP-1"       -- tela do notebook
MONITOR3 = ""
PRIMARY_MONITOR = MONITOR1  -- força externo como principal
```

#### Passo 3: Configurar `~/.config/hypr/config/monitors.lua`
```lua
hl.monitor({
    output    = MONITOR1,
    mode      = "preferred",   -- usa resolução/refresh nativa do monitor (ex: 2560x1440@155)
    position  = "0x0",         -- primário na esquerda
    scale     = "1",
})

hl.monitor({
    output    = MONITOR2,
    mode      = "preferred",
    position  = "1920x0",      -- secundário à direita do primário
    scale     = "1.5",
    mirror    = "HDMI-A-1",    -- espelha o externo (opcional: remova para estender)
})
```

#### Passo 4: Ativar VRR/FreeSync em `~/.config/hypr/config/misc.lua`
```lua
hl.config({
    misc = {
        vrr = 3,   -- 0=desligado, 1=apenas fullscreen, 2=sempre que possível, 3=sempre forçado (gaming)
        ...
    },
})
```

#### Passo 5: Aplicar sem reiniciar
```bash
hyprctl reload
```

## 13. Áudio

| Ordem | Pacote | Comando | Pra quê |
|---|---|---|---|
| 13.1 | **easyeffects** | `sudo pacman -S easyeffects` | Equalizador de sistema — EQ paramétrico, Bass Boost, compressor, reverb, surround |
| 13.2 | **pipewire** | já incluso no CachyOS | Servidor de áudio (substitui PulseAudio/JACK) |
| 13.3 | **pipewire-pulse** | já incluso | Compatibilidade com apps PulseAudio |

### Configuração Rápida

1. Abrir EasyEffects → aba **Output**
2. Clicar **Add Effect** → **Equalizer**
3. Escolher preset (ex: "Loudness", "Rock", "Electronic", "Bass Boost")
4. Ou ajustar bandas manualmente clicando no gráfico

O equalizador aplica automaticamente sobre **toda** saída de áudio (Spotify, navegador, player de música, etc).

