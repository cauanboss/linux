# Instalações — Arch Linux

Registro cronológico de ferramentas instaladas no Arch Linux (CachyOS).

- 2026-06-27 — kilo — gerenciador de projetos AI
- 2026-06-27 — n (tj/n) — gerenciador de versões do Node.js
- 2026-06-27 — steam — plataforma de jogos
- 2026-06-27 — zsh — shell alternativo
- 2026-06-27 — ohmyzsh — gerenciador de configuração do zsh
- 2026-06-27 — zsh-autosuggestions — plugin de sugestões de comandos
- 2026-06-27 — zsh-syntax-highlighting — plugin de highlights de sintaxe
- 2026-06-27 — docker — engine de containers
- 2026-06-27 — wl-clipboard — clipboard para Wayland / brilho de teclado
- 2026-06-27 — datagrip — IDE JetBrains para bancos de dados + .desktop entry
- 2026-06-27 — brave — navegador web
- 2026-06-27 — cursor — editor de código com IA
- 2026-06-28 — secrets (org.gnome.World.Secrets) — gerenciador de senhas GNOME
- 2026-06-28 — transmission — BitTorrent client
- 2026-07-05 — gear-lever — gerenciador de AppImages
- 2026-07-05 — warehouse — gerenciador de Flatpaks
- 2026-07-07 — zash terminal (zashterminal) — terminal emulator (padrão, substitui terminus)
- 2026-07-07 — mission center (io.missioncenter.MissionCenter) — monitor de sistema
- 2026-07-07 — flatsweep (io.github.giantpinkrobots.flatsweep) — limpeza de resíduos de Flatpaks desinstalados
- 2026-07-07 — ente auth (io.ente.auth) — autenticador 2FA
- 2026-07-07 — localsend (org.localsend.localsend_app) — compartilhamento de arquivos na rede local
- 2026-07-07 — linux toys (linuxtoys) — conjunto de utilitários Linux via `linux.toys/install.sh`
- 2026-07-09 — rtk (rtk-ai/rtk) — CLI proxy que reduz consumo de tokens LLM em 60-90%
- 2026-07-12 — firefox — navegador web
- 2026-07-12 — paru — AUR helper alternativo
- 2026-07-13 — rclone — sync de cloud (Google Drive, etc)
- 2026-07-13 — rclone-ui-bin — interface gráfica pro rclone
  - Problema: rclone-ui dava erro 401 Unauthorized e não conectava
  - Causa: conflito entre o serviço systemd `rclone-rcd.service` (com auth `rclone:rclone`) e o daemon que o rclone-ui tenta iniciar (`--rc-no-auth` na porta 5572)
  - Solução:
    1. Desabilitar o serviço conflitante: `systemctl --user disable --now rclone-rcd.service`
    2. Remover `~/.config/systemd/user/rclone-rcd.service` (evita reativar por engano)
    3. Corrigir handler OAuth em `~/.local/share/applications/rclone-ui-handler.desktop` — `Exec=rclone-ui %u` (estava apontando pro Cursor)
    4. Se abrir e fechar de novo der problema, matar daemon órfão: `pkill -f "rclone rcd"` e reabrir
    5. (Opcional) agendamento de tarefas: `sudo pacman -S cronie && systemctl enable --now cronie.service`
- 2026-07-13 — unzip — descompactador ZIP
- 2026-07-14 — alacritty — terminal emulator (GPU)
- 2026-07-14 — btop — monitor de sistema via terminal
- 2026-07-14 — fastfetch — info do sistema (alternativa ao neofetch)
- 2026-07-14 — kitty — terminal emulator (GPU)
- 2026-07-14 — meld — diff/merge visual
- 2026-07-14 — micro — editor de texto via terminal
- 2026-07-14 — ripgrep (rg) — busca rápida em código/arquivos
- 2026-07-14 — dolphin — gerenciador de arquivos KDE
- 2026-07-14 — openssh — servidor/cliente SSH

- 2026-07-15 — datagrip (Toolbox) — IDE JetBrains para bancos de dados via Toolbox oficial
  - Problema: senhas/credenciais não persistiam após reiniciar o DataGrip no Hyprland (CachyOS)
  - Causa: nenhum serviço de keyring rodando (gnome-keyring/kwalletd não iniciados no Hyprland)
  - Solução:
    1. Abrir Settings → Appearance & System Settings → Passwords
    2. Alterar de "Forget on restart" para "In KeePass"
    3. Definir path do arquivo: ~/.config/JetBrains/DataGrip2026.1/c.kdbx
    (sem a barra no final — é um arquivo, não diretório)
  - Alternativa: instalar gnome-keyring e adicionar ao autostart do Hyprland:
    `exec-once = /usr/bin/gnome-keyring-daemon --start --components=secrets`

- 2026-07-18 — sddm-astronaut-theme — tema de login SDDM para Hyprland (substitui sddm-nordic-theme-git e catppuccin-sddm-theme-mocha)
  - Repo: https://github.com/Keyitdev/sddm-astronaut-theme (3.1k stars, Qt6, zero deps KDE)
  - Instalação: `yay -S sddm-astronaut-theme` (dependência: qt6-virtualkeyboard)
  - Config SDDM:
    - `/etc/sddm.conf.d/wayland.conf` → `DisplayServer=wayland` (necessário para Hyprland)
    - `/etc/sddm.conf.d/theme.conf` → `Current=sddm-astronaut-theme`
  - Sub-temas disponíveis em `/usr/share/sddm/themes/sddm-astronaut-theme/Themes/`:
    - astronaut (padrão), black_hole, cyberpunk, japanese_aesthetic, pixel_sakura, purple_leaves, post-apocalyptic_hacker, hyprland_kath
  - Para trocar sub-tema: editar `ConfigFile=` no arquivo `/usr/share/sddm/themes/sddm-astronaut-theme/metadata.desktop`
  - Preview: `sddm-greeter-qt6 --test-mode --theme /usr/share/sddm/themes/sddm-astronaut-theme/`
  - Sub-tema atual: hyprland_kath (ConfigFile=Themes/hyprland_kath.conf)

- 2026-07-18 — ssh-agent (systemd user service) — agente SSH único para toda sessão no fish shell + Hyprland
  - Serviço: `~/.config/systemd/user/ssh-agent.service` → Type=forking, socket em `$XDG_RUNTIME_DIR/ssh-agent.socket`
  - Fish: `~/.config/fish/config.fish` → carrega `~/.ssh/agent.env` automaticamente
  - Comandos úteis:
    - `systemctl --user status ssh-agent` — verificar status
    - `ssh-add ~/.ssh/<chave>` — adicionar chave (vale pra sessão inteira)
    - `ssh-add -l` — listar chaves carregadas
