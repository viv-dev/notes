# vscode

## ZSH/Powerline Fonts in VSCode Terminal

On manjaro, you can install a compatible terminal font from the community repos:

```bash
sudo pacman -S ttf-meslo-nerd-font-powerlevel10k
```

Once installed, you can then set the terminal font in the settings page or directly in the settings json:

```json
{
  "terminal.integrated.fontFamily": "MesloLGS NF"
}
```
