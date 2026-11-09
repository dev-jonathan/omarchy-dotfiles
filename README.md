# omarchy-dotfiles

Configurações pessoais do Omarchy. Atualmente inclui o preset e o autoload do input-remapper.

## Input Remapper

Configuração validada com `input-remapper-git 2.2.1.r18.gd18d486d-1`, Python 3.14 e kernel `6.18.57-2-lts`.

- Dispositivo: `SINOWEALTH 2.4G Wireless Receiver`.
- Preset: `Turbo Scroll`.
- Mapeamento: manter o botão lateral de código `276` pressionado e mover o mouse verticalmente para rolar.
- Saída: roda vertical (`REL_WHEEL`, código `8`), com ganho `-12`.

| Arquivo | Função |
| --- | --- |
| `.config/input-remapper-2/config.json` | Associa o dispositivo ao preset para carregamento automático. |
| `.config/input-remapper-2/presets/SINOWEALTH 2.4G Wireless Receiver/Turbo Scroll.json` | Define a combinação de entrada e a saída de rolagem. |
| `.local/bin/input-remapper-autoload` | Aguarda 5 segundos e executa o autoload com `/usr/bin/python3`. |
| `.config/autostart/input-remapper-autoload.desktop` | Executa o script ao iniciar a sessão gráfica. |

## Restaurar

Instale o pacote e habilite o serviço:

```bash
omarchy pkg aur add input-remapper-git
sudo systemctl enable --now input-remapper.service
```

Faça backup das configurações existentes. Na raiz deste repositório, copie os arquivos:

```bash
mkdir -p "$HOME/.config/autostart"
cp -a .config/input-remapper-2 "$HOME/.config/"
install -Dm755 .local/bin/input-remapper-autoload "$HOME/.local/bin/input-remapper-autoload"
cp .config/autostart/input-remapper-autoload.desktop "$HOME/.config/autostart/"
```

O campo `Exec` do arquivo `.desktop` aponta para `/home/devjonathan/.local/bin/input-remapper-autoload`. Ajuste esse caminho se o diretório pessoal for diferente. Em outro dispositivo, revise o nome do receptor e o `origin_hash` do preset.

Carregue a configuração e consulte os logs:

```bash
~/.local/bin/input-remapper-autoload
systemctl status input-remapper.service
journalctl -b -t input-remapper-autoload --no-pager
```

O script usa o Python do sistema explicitamente para evitar que ferramentas de gerenciamento de runtimes alterem o interpretador utilizado. O autoload funciona; há uma falha preexistente de importação Python no ícone da bandeja (`input-remapper-tray`) que não é corrigida por estes arquivos.

## Alterações com agentes

Recomenda-se usar a skill `$omarchy` ao pedir alterações nestas configurações. Na instalação local, as instruções ficam em `/usr/share/omarchy/default/agents/skills/omarchy/SKILL.md`.

Mantenha as personalizações em `~/.config/` e `~/.local/`. Faça backup antes de aplicar mudanças e não edite os arquivos distribuídos em `/usr/share/omarchy/`.
