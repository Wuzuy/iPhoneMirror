<p align="center">
  <img src="src/App/Assets/iPhoneMirror.png" width="112" alt="Ícone do iPhoneMirror">
</p>

<h1 align="center">iPhoneMirror</h1>

<p align="center">
  Espelhamento de tela e áudio do sistema do iPhone para Windows com baixa latência.<br>
  Captura direta via USB e recepção AirPlay sem fio em um único aplicativo.
</p>

<p align="center"><a href="README.md">简体中文</a> · <a href="README.en.md">English</a> · <strong>Português (Brasil)</strong></p>

> [!NOTE]
> Tradução comunitária para o português (Brasil) a partir da versão em inglês.
> Em caso de divergência, vale o `README.en.md`.

<p align="center">
  <a href="https://github.com/RayrenSX/iPhoneMirror/releases"><img alt="GitHub Release" src="https://img.shields.io/github/v/release/RayrenSX/iPhoneMirror?include_prereleases&sort=semver"></a>
  <a href="https://github.com/RayrenSX/iPhoneMirror/actions/workflows/windows-build.yml"><img alt="Windows build" src="https://github.com/RayrenSX/iPhoneMirror/actions/workflows/windows-build.yml/badge.svg"></a>
  <a href="LICENSE"><img alt="GPL v3 License" src="https://img.shields.io/badge/license-GPL--3.0--only-3DA639.svg"></a>
  <img alt="Windows 10 and 11 x64" src="https://img.shields.io/badge/Windows-10%20%7C%2011-0078D4">
</p>

> [!IMPORTANT]
> Esta é uma prévia pública e não possui assinatura comercial Authenticode, então o Windows
> pode exibir avisos do SmartScreen ou de editor desconhecido. O Apple Screen Capture usa um
> protocolo privado e pode exigir atualizações para futuras versões do iOS. As compilações oficiais
> atualmente suportam apenas Windows x64; o Windows ARM64 não é suportado porque o driver
> USB de kernel e o runtime sem fio não estão disponíveis para ARM64.

> [!TIP]
> ### Agradecimento especial: porte para Linux
>
> Um agradecimento especial a **[@furruka](https://github.com/furruka)** por criar e
> manter um porte nativo para Linux baseado neste projeto, no
> [branch dedicado de adaptação para Linux](https://github.com/furruka/iPhoneMirror). Esse trabalho estende
> os caminhos de espelhamento USB e AirPlay do iPhoneMirror para o Linux, substituindo as camadas
> específicas do Windows (GUI, renderização, áudio, decodificação de vídeo, comunicação USB e descoberta de dispositivos)
> por equivalentes nativos, buscando preservar o comportamento de protocolo e de política do projeto original.
>
> O porte ainda está em desenvolvimento ativo e atualmente não oferece um pacote de lançamento
> utilizável para Linux. Consulte as [notas do porte para Linux](https://github.com/furruka/iPhoneMirror/blob/linux-port/docs/LINUX_PORT.md)
> para saber o status atual, as instruções de compilação e as limitações conhecidas. Somos muito gratos
> pelo tempo e pela contribuição de furruka, e incentivamos usuários Linux a acompanhar e apoiar
> esse trabalho.

## Download

A versão atual é a `v1.8.3`. Baixe o `iPhoneMirror-Setup-v*-x64.exe` em
[Releases](https://github.com/RayrenSX/iPhoneMirror/releases). O assistente de instalação
em três idiomas permite escolher o destino, usa por padrão
`C:\Program Files\iPhoneMirror` na instalação como administrador, cria entradas no menu
Iniciar e oferece um atalho opcional na área de trabalho. A instalação por usuário usa o
diretório de programas do usuário do Windows. Para uso portátil, baixe o
`iPhoneMirror-v*-win-x64.zip`, extraia-o por completo e execute o
`iPhoneMirror.exe`. Se o Windows relatar **Imagem inválida** ou `0xc0e90002` para alguma
DLL sem fio, use o instalador Setup. Se o uso portátil for indispensável, abra as Propriedades
do ZIP baixado, selecione **Desbloquear** e extraia novamente; desbloquear apenas uma
DLL já extraída não é suficiente.

Ambos os pacotes são autocontidos e incluem o gerenciador de drivers independente
`iPhoneMirror.Driver.exe`. Eles não exigem .NET Desktop Runtime separado nem download
adicional de ferramenta de driver. Verifique os downloads com o arquivo
`SHA256SUMS.txt` do mesmo Release.

Por padrão, o app verifica os GitHub Releases após iniciar. Quando há atualização
disponível, ele mostra a versão, a data de publicação e as notas de lançamento em Markdown;
**Atualizar agora** baixa, verifica e inicia a atualização local, depois
reinicia o app. A página Sobre também oferece verificação manual, controles de canal
estável/Beta e downloads automáticos. Os temas do app são configurados nas preferências
do app, na janela principal. Falhas e tempos de rede nunca bloqueiam a inicialização normal.

O [guia completo do usuário (em chinês)](docs/USER_GUIDE.md) cobre todas as interfaces e fluxos principais.

O computador precisa do suporte USB da Apple. Se estiver ausente, o gerenciador de drivers primeiro
usa um `AppleMobileDeviceSupport64.msi` local confiável, depois baixa o MSI independente
do catálogo Apple Software Update e, por fim, recorre ao pacote oficial assinado do iTunes
para extrair seu componente Apple Mobile Device Support. O Apple Devices da Microsoft Store
continua sendo uma opção manual suportada; binários da Apple não são redistribuídos por este
projeto. A descoberta sem fio usa o suporte a DNS-SD embutido no Windows 10/11 e não instala o Bonjour;
a descoberta em si não exige acesso de administrador. A instalação Setup como administrador
adiciona a regra de firewall `iPhoneMirror Wireless AirPlay` para a sub-rede local. A regra é
limitada ao `WirelessHost.exe` e à sub-rede local, liberando as portas de mídia TCP/UDP
negociadas por sessão AirPlay; ela é removida ao desinstalar. Pacotes portáteis não alteram
o firewall automaticamente.

## Descrição do projeto

O iPhoneMirror é uma ferramenta local de espelhamento de iPhone/iPad para Windows 10/11 x64. Ele mantém
a captura USB com fio e a recepção AirPlay da rede local sob um mesmo fluxo de sessão,
prévia, áudio, captura de tela, janela separada, OBS e múltiplos dispositivos,
sem retransmissão em nuvem.

O projeto tem quatro fronteiras explícitas: o núcleo C++ cuida do protocolo USB privado
da Apple, da análise QuickTime/CoreMedia, da decodificação H.264, da renderização D3D11 e do
áudio WASAPI; o app WPF cuida da descoberta de dispositivos, do controle de sessão e da interface; e um
host sem fio isolado cuida do protocolo AirPlay e da decodificação, enviando quadros de mídia limitados
por um named pipe. Instalação, reparo e remoção de drivers pertencem ao
`iPhoneMirror.Driver.exe` independente; o app principal apenas lê o estado dos drivers com fio
e nunca altera drivers do sistema dentro do processo de captura.

> [!TIP]
> Consulte o [guia completo do usuário](docs/USER_GUIDE.md) para configuração de drivers,
> USB, AirPlay, prévias de múltiplos dispositivos, OBS, logs e configurações avançadas.

## Destaques

### Apresentação específica por modelo de iPhone e iPad

O iPhoneMirror não coloca todos os dispositivos no mesmo retângulo genérico com cantos arredondados.
Ele converte o `ProductType` da Apple em perfis visuais para iPhone X, modelos com notch,
mini, padrão/Max, Dynamic Island, iPad Pro, Air, mini e iPads base de tela cheia. Telas conhecidas
com botão Home e telas retangulares permanecem retangulares. Dispositivos futuros desconhecidos usam
uma geometria conservadora de fallback para que um iPad não seja cortado com curva de telefone.

O perfil afeta tanto a renderização nativa quanto o contorno da janela separada.
Redimensionamento, mudanças de orientação e transições de tela cheia preservam a forma visual
daquele dispositivo, e o menu de contexto ainda permite remover ou restaurar os cantos manualmente.
Os parâmetros de canto são ajustes visuais baseados na aparência pública dos dispositivos e na geometria
dos quadros, não medidas industriais publicadas pela Apple.

### Transporte unificado, renderização nativa e múltiplos dispositivos

- O Screen Capture via USB e o AirPlay da rede local compartilham o mesmo fluxo de dispositivo, prévia, áudio e OBS.
- Cada telefone tem sua própria sessão e janela separada; vários dispositivos podem continuar rodando juntos.
- Os cartões de dispositivo suportam reordenação por toque longo, e um novo emissor sem fio é selecionado automaticamente apenas uma vez.
- Quadros de H.264/CoreMedia e de espelhamento AirPlay são decodificados localmente e apresentados via D3D11/DirectComposition.
- A mídia não passa por retransmissão em nuvem do iPhoneMirror, e a captura USB não depende da rede.
- Janelas separadas limpas estão prontas para o OBS, e as capturas de tela leem o quadro decodificado sem a interface do aplicativo.
- Controle opcional de mouse/teclado via BLE HID com o AssistiveTouch do iOS, sem app no telefone nem jailbreak.

### Comparado com ferramentas comuns de espelhamento

| Dimensão | iPhoneMirror | Abordagem comum genérica |
|---|---|---|
| Conexões | USB e AirPlay em um só fluxo | Um transporte, ou apps separados com e sem fio |
| Formato do dispositivo | Curvas e cantos por família de iPhone/iPad | Um raio genérico, retângulo ou moldura preta extra |
| Múltiplos dispositivos | Sessões, janelas, ordenação e prévia simultânea independentes | Troca focada em um único dispositivo |
| Compatibilidade com fio | Modos Demo, AirPlay experimental e Aisi por dispositivo | Parâmetros fixos de negociação |
| OBS | Janela separada nativa e limpa | Recortar a interface de controle ou capturar a área de trabalho |
| Drivers | Instalação, reparo, remoção e logs separados por dispositivo | Alterações de driver ocultas dentro do app principal |
| Caminho dos dados | Processamento local no PC/rede local, sem retransmissão em nuvem do projeto | Alguns produtos exigem contas ou serviços online |

O controle Bluetooth opcional do iPhoneMirror usa BLE HID mais o AssistiveTouch do iOS;
limita-se a entrada estilo ponteiro e não injeta eventos multitoque. O
projeto não tem editor de vídeo integrado. O protocolo privado da Apple e a implementação
compatível de AirPlay podem exigir atualizações para futuras versões do iOS.

## Recursos

| Área | Implementação |
|---|---|
| Captura com fio | USB direto com modos Demo, AirPlay experimental e compatível com Aisi por dispositivo |
| Captura sem fio | AirPlay da rede local integrado à prévia principal e a todos os recursos de saída |
| Transmissão de apps de vídeo | Reprodução AirPlay/DLNA HTTP(S)/HLS, controles e áudio de origem |
| Vídeo | H.264 CoreMedia/AVCC, análise de descrição HEVC e políticas automática/hardware/software do Media Foundation |
| Renderização | Prévia nativa D3D11/DirectComposition com metadados BT.709 de faixa completa |
| Áudio | PCM USB 48 kHz e PCM AirPlay com reprodução, mudo e volume via WASAPI |
| Dispositivos | Metadados de iPhone/iPad, status de confiança, atualização estável e troca segura |
| Qualidade | Limites locais Nativo/1080p/720p/540p e limites de 24/30/60/120 FPS |
| Prévia | Principal, separada, tela cheia, rotação, trava de proporção e cantos por dispositivo |
| OBS | Janela separada limpa por dispositivo para Captura de Janela |
| Controle Bluetooth | Vinculação BLE HID de mouse/teclado por dispositivo, navegação do sistema e atalhos globais configuráveis |
| Ajustes de imagem | Brilho, contraste, saturação e gama (somente prévia) |
| Ferramentas | Captura de tela, atualização forçada, atalhos, logs ao vivo e interface em chinês simplificado, chinês tradicional (Hong Kong), inglês e português (Brasil) |
| Driver | Verificação rigorosa por dispositivo antes da captura com fio; abre o gerenciador de drivers independente em caso de falha |

As opções de resolução e FPS limitam apenas a apresentação local; não reduzem a
qualidade original do fluxo USB.

Dispositivos USB usam por padrão o **A Demo (recomendado)**, que anuncia
`Valeria=true` com o `DisplaySize` nativo e preserva o enquadramento completo do telefone, mas trava data,
hora e bateria da barra de status nos valores de demonstração da Apple. O **B AirPlay (experimental)** usa
dimensões nativas e orientação adaptativa para que apps de vídeo possam usar reprodução
externa, com possível corte ou enquadramento incompleto. O **C Modo Aisi** fixa
o alvo em 1565×1565 para uma negociação previsível, ao custo da nitidez de
origem. O botão de exclamação ao lado de cada modo mostra todas as vantagens e desvantagens, e
a seleção vale apenas para o dispositivo USB atual.

## Início rápido

1. Execute o Setup do Release e abra o iPhoneMirror pelo menu Iniciar. No
   pacote portátil, extraia o ZIP por completo e execute o `iPhoneMirror.exe`.
2. Conecte o iPhone ou iPad via USB, desbloqueie-o e escolha **Confiar Neste Computador**.
3. Clique em **Gerenciador de drivers** na navegação à esquerda e execute a instalação em um clique para o
   dispositivo desejado. A ferramenta instala o suporte USB da Apple ausente e o filtro
   de captura conforme necessário.
4. Selecione o telefone e clique em **Iniciar espelhamento**. Se o dispositivo com fio selecionado
   estiver com driver ausente ou inválido, o app cancela a tentativa e abre
   o gerenciador de drivers automaticamente.

Selecionar outro dispositivo primeiro envia os controles de parada QuickTime para a sessão
anterior e restaura sua configuração USB normal. Fechar a janela principal executa
o mesmo caminho de limpeza.

> [!WARNING]
> Não use o Zadig para substituir o driver pai da Apple por WinUSB/libusb.
> O iPhoneMirror inclui apenas o runtime de modo usuário `libusb0.dll` necessário para
> iniciar o aplicativo. Ele não instala nem habilita o filtro de captura de
> kernel; use o utilitário de drivers separado para qualquer alteração de UpperFilter `libusb0`.

## Gerenciamento de drivers com fio

O `iPhoneMirror.exe` apenas lê o estado dos drivers. Instalação, reparo e remoção são
feitos pelo `iPhoneMirror.Driver.exe` independente, na mesma pasta.
O botão **Gerenciador de drivers** na navegação à esquerda abre-o a qualquer momento; se a
ferramenta já estiver em execução, a janela existente é ativada.

Ao clicar em **Iniciar espelhamento** para um dispositivo com fio, o app verifica o
telefone selecionado antes de criar a sessão de captura:

- o pai USB da Apple ainda usa `usbccgp`;
- o dispositivo tem o UpperFilter `libusb0`;
- o arquivo e o serviço `libusb0.sys` estão íntegros; e
- o `libusb0` consegue enumerar o serial exato do dispositivo.

Qualquer falha bloqueia a tentativa com fio e abre o gerenciador de drivers. Depois de
reparar o driver e reconectar o dispositivo quando solicitado, volte ao
app principal e clique em **Iniciar espelhamento** novamente. Os logs da interface ficam em
`%LOCALAPPDATA%\iPhoneMirror.Driver\Logs\driver-ui.log`; os logs de operação elevada
ficam em `%ProgramData%\iPhoneMirror.Driver`.

O inventário completo de drivers embutidos/externos está documentado em
[`docs/DRIVER_DEPENDENCIES.md`](docs/DRIVER_DEPENDENCIES.md).

## Logs de diagnóstico

O app principal grava erros de interface e de fluxo em
`%LOCALAPPDATA%\iPhoneMirror\Logs\application.log`. Diagnósticos de núcleo USB, decodificador e renderização
ficam ao lado, em `capture.log`. Falhas de inicialização também gravam
`startup.log`, e atualizações em um clique criam arquivos
`installer-update-*.log` com data e hora. Se o LocalAppData estiver temporariamente indisponível,
erros gerenciados críticos usam `%TEMP%\iPhoneMirror-fallback.log`.

Use **Sobre → Diagnósticos** para abrir a pasta de logs ou limpar logs e pacotes de
atualização baixados imediatamente. Os logs giram automaticamente, arquivos com mais de 14
dias são removidos e o diretório principal de logs é limitado a 64 MB. Arquivos em
uso são ignorados sem interromper o espelhamento.

> [!NOTE]
> Essa verificação automática vale apenas para dispositivos USB com fio. Fontes AirPlay
> sem fio não exigem nem inspecionam o `libusb0`, e nunca abrem o gerenciador
> de drivers por estado de driver.

## AirPlay sem fio

O receptor AirPlay inicia com o aplicativo principal. Não é preciso
clicar em **Iniciar espelhamento** para que o receptor apareça no iPhone. Nenhuma fonte
AirPlay vazia é mostrada na lista de dispositivos à esquerda; um cartão sem fio é criado
após um emissor conectar e é selecionado automaticamente uma vez, no momento da conexão.

1. Conecte o computador Windows e o iPhone/iPad na mesma rede privada.
2. Configure o nome do receptor e o perfil de conexão anunciado na
   seção **AirPlay sem fio**.
3. Escolha máximo 5120x2880 a 60 fps, 1080p padrão a 60 fps, 720p a 30 fps
   ou 540p a 30 fps.
4. Clique em **Aplicar**. Mudanças de nome e resolução são resumidas em uma caixa de diálogo.
5. Abra o Espelhamento de Tela na Central de Controle do iOS e selecione o nome configurado.
6. Use **Parar espelhamento** para uma sessão sem fio conectada; o receptor continua rodando.

As abas sem fio não mostram os limites locais de resolução/FPS com fio. A qualidade AirPlay
precisa ser anunciada antes da conexão. Aplicar um novo nome ou perfil reinicia
o receptor, desconecta todas as sessões sem fio atuais e exige que o telefone
selecione o receptor novamente. Sessões de espelhamento sem fio ainda suportam
volume, capturas de tela, janelas separadas/em tela cheia, prévias simultâneas e OBS.

Não há um número fixo de dispositivos sem fio no nível do aplicativo; a capacidade prática
depende de CPU/GPU, memória e banda da rede local.
A mesma identidade do receptor também atende transmissão AirPlay/DLNA de apps de vídeo. Portas fixas
de controle/descoberta são RAOP `5001`, AirPlay `7001`, DLNA `8090` e SSDP
`1900`; portas de espelhamento e de mídia RAOP são negociadas dinamicamente por sessão.

Uma instalação Setup como administrador cria a regra de firewall de sub-rede local
`iPhoneMirror Wireless AirPlay` para o host sem fio. Ela tem escopo para o processo do host e a sub-rede
local, mas libera as portas dinâmicas de mídia TCP/UDP retornadas durante o `SETUP` do AirPlay;
é removida ao desinstalar. Instalações existentes precisam de um pacote
contendo essa correção para migrar a regra antiga. Um pacote portátil não altera
o firewall automaticamente. Adicione manualmente a regra equivalente com escopo de processo em uma
rede confiável quando conexões AirPlay ou DLNA de entrada forem bloqueadas.

Se um emissor conectar em tela preta e cair após cerca de dez segundos, verifique
se ambas as regras mostram `LocalPort: Any` e apontam para o
`Wireless\iPhoneMirror.WirelessHost.exe` atual; esse padrão significa que a conexão de
controle funcionou, mas a porta negociada do espelhamento foi bloqueada.

Durante o handshake de `SETUP` do AirPlay, o receptor lê `deviceID`, `model` (ProductType da Apple, como `iPhone9,1`) e `osVersion`
do emissor a partir do plist binário. Os valores cruzam o IPC versionado por named pipe como mensagem
`DeviceInfo` e aparecem no painel do dispositivo selecionado. ProductTypes conhecidos
são exibidos como modelo legível, mantendo o identificador original entre parênteses;
identificadores desconhecidos são mostrados como estão.

## Transmissão de música via AirPlay

Um iPhone ou iPad pode enviar música para o Windows sem iniciar o Espelhamento de Tela:

1. Mantenha o computador e o iPhone/iPad na mesma rede privada e abra o iPhoneMirror.
2. Toque no botão de áudio AirPlay em um app de música ou no painel Em Execução da Central de Controle.
3. Selecione o mesmo nome de receptor usado para o Espelhamento de Tela.
4. O iPhoneMirror cria uma fonte sem fio automaticamente e toca o áudio PCM. Use
   os controles principais de áudio para mudar o volume, silenciar ou encerrar a sessão.

Uma sessão somente de áudio não tem vídeo. A prévia mostra o estado **Música AirPlay**,
e as ferramentas de captura de tela, prévia separada e tela cheia ficam desabilitadas até o emissor
começar a entregar vídeo.

## Transmissão de apps de vídeo

Apps de vídeo usam a mesma identidade do receptor AirPlay, mas enviam uma URL de reprodução em vez
de quadros de espelhamento. Selecione o receptor no botão Transmitir/AirPlay do
app; o iPhoneMirror abre uma janela separada de reprodução e suporta vídeo
HTTP(S) comum e HLS, controles de reprodução, gravação, transmissão e câmera virtual.
URLs de reprodução protegidas por DRM, vinculadas a login ou privadas podem não estar disponíveis
para um receptor de terceiros.

## Controle reverso via Bluetooth

Ative o AssistiveTouch do iOS e use um adaptador Bluetooth do Windows com suporte a modo
periférico BLE. Na primeira conexão, confirme o telefone correspondente na
caixa de diálogo de vinculação de cliente Bluetooth; as vinculações são salvas por dispositivo espelhado e podem
ser removidas nas Configurações. A janela de atalhos configura controle Bluetooth, com fio e
sem fio, Central de Controle, Central de Notificações, Alternador de Apps, Início, tecla Boss,
volume, tela de bloqueio, Dock e ações da Siri. F12 é reservado; F1–F11 e
botões direito/central do mouse podem ser capturados diretamente.
Ações não vinculadas ficam desabilitadas, vinculações duplicadas são rejeitadas e `Backspace`
ou `Delete` limpa uma vinculação. A tecla padrão do controle Bluetooth é `F9` e a
tecla Boss padrão é `Ctrl+Alt+B`.

O controle reverso com e sem fio usa o `iUsbBridge.exe` incluído. O controle com fio
exige dispositivo desbloqueado e confiável, além do Modo Desenvolvedor quando solicitado;
a Developer Disk Image correspondente é resolvida e verificada a partir de metadados oficiais do GitHub
no primeiro uso. O controle sem fio exige pareamento e acessibilidade na rede local.
A primeira conexão USB ou AirPlay abre a orientação de perfis de dispositivo
para que ambas as identidades sejam associadas ao mesmo telefone.

## Dependências de terceiros e licenciamento

| Dependência | Finalidade | Licença/origem |
|---|---|---|
| .NET 10, WPF, Windows SDK | Interface, APIs do Windows e runtime de publicação | Runtime oficial da Microsoft |
| libusb 1.0.29 | Camada opcional de compatibilidade de transporte USB | LGPL-2.1-or-later, `third_party/libusb/` |
| libusb-win32 1.2.6.0 | Driver de filtro `libusb0` usado pelo gerenciador independente | LGPL-3.0 e termos upstream, `src/DriverInstaller/Assets/` |
| AirPlayServer 1.1.2 | AirPlay sem fio, FairPlay, decodificação de vídeo e áudio | GPL-3.0, LGPL-2.1-or-later e termos upstream, `third_party/airplay-server/` |
| Runtime FFmpeg 4.4.2 | Runtime H.264/áudio embutido do AirPlayServer | LGPL-2.1-or-later, distribuído com o AirPlayServer |
| Runtime FFmpeg 8.1.2 | Gravação, saída ao vivo e ponte HLS de transmissão de mídia | GPL-3.0, incluído por padrão em `tools/ffmpeg/` |
| Fixtures quicktime_video_hack | Vetores de regressão do protocolo QuickTime | MIT, apenas fixtures de teste |

Apple Devices, Apple Mobile Device Support, iTunes e componentes do sistema
Windows são pré-requisitos externos e não são redistribuídos por este
projeto. Consulte [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) e o
`SOURCE.md` do AirPlayServer para direitos autorais, origem, versão, hash e detalhes de
licença.

## OBS

Abra a prévia separada de USB ou AirPlay e selecione sua janela iPhoneMirror
na Captura de Janela do OBS. A Captura de Gráficos do Windows é recomendada no
Windows 11. Consulte [OBS_OUTPUT.md](docs/OBS_OUTPUT.md).

## Dispositivos verificados

| ProductType / iOS | Quadro nativo | Resultado medido |
|---|---:|---|
| `iPhone18,3` / iOS 26.5.2 | 1206×2622 | ~58,6 FPS, decodificação típica 3–5 ms, PCM estéreo 48 kHz |
| `iPhone13,1` / iOS 18.7.8 | 1082×2340 | ~58,9 FPS, decodificação típica 3–6 ms, PCM estéreo 48 kHz |

São combinações testadas, não garantia para todo iPhone ou build do iOS.

## Compilar a partir do código-fonte

Requisitos: Windows 10/11 x64, Visual Studio 2026 Build Tools com MSVC,
Windows SDK e CMake, .NET 10 SDK com suporte a Windows Desktop, e MSYS2
UCRT64 com CMake, Ninja, toolchain UCRT64, GStreamer (base, good, bad, libav),
libplist e OpenSSL para o fallback UxPlay incluído.

```powershell
git clone https://github.com/RayrenSX/iPhoneMirror.git
cd iPhoneMirror
./build.ps1 -Configuration Release
```

O script compila o núcleo C++20, executa testes de protocolo e publica o
aplicativo WPF autocontido em `outputs/iPhoneMirror`, incluindo:

```text
outputs/iPhoneMirror/iPhoneMirror.exe
outputs/iPhoneMirror/iPhoneMirror.Driver.exe
outputs/iPhoneMirror/iPhoneMirror.Core.dll
outputs/iPhoneMirror/iPhoneMirror.VirtualCamera.dll
outputs/iPhoneMirror/iPhoneMirror.VirtualCamera.Admin.exe
outputs/iPhoneMirror/tools/ffmpeg/ffmpeg.exe
outputs/iPhoneMirror/Wireless/iPhoneMirror.WirelessHost.exe
outputs/iPhoneMirror/Wireless/UxPlay/iPhoneMirror.UxPlayHost.exe
outputs/iPhoneMirror/Wireless/UxPlay/uxplay.exe
```

O `outputs/iPhoneMirror` é a compilação portátil com dependências .NET/WPF embutidas
nos executáveis. O instalador usa `outputs/iPhoneMirror.Installer`,
onde app e gerenciador de drivers compartilham DLLs externas de runtime para reduzir o tamanho
do download.

A compilação padrão inclui o runtime FFmpeg 8.1.2 de saída de mídia para que gravação e
transmissão RTMP/SRT/WHIP funcionem de fábrica. Compile a edição compacta apenas
quando o tamanho mínimo for necessário e uma dependência de FFmpeg do sistema for aceitável:

```powershell
.\build.ps1 -Configuration Release -OmitMediaOutputRuntime
```

Passe `-OmitMediaOutputRuntime` também para o script de empacotamento do release ao
publicar a edição compacta.

Compile todos os artefatos do Release (Setup, ZIP, checksums e SBOM):

```powershell
./scripts/package_release.ps1 -Version 1.8.3 -GenerateSbom
```

Passe `-UpdateReleaseManifest` ao produzir os artefatos que serão enviados.
O script de release então sincroniza tamanhos e digests SHA256 para a entrada
correspondente em `updates/releases.json`, mantendo válido o endpoint alternativo de atualização.
Compilações locais comuns de pacotes não modificam o manifesto publicado de releases.

O script baixa o Inno Setup 6.7.3 com hash fixo e suas traduções em chinês simplificado e
tradicional para `work/tools`; nenhuma instalação global do Inno Setup é necessária.

Compile e execute as suítes de teste sem publicar o app autocontido:

```powershell
./build.ps1 -Configuration Debug -NoPublish
```

## Arquitetura

```text
iPhone/iPad
  ├─ USB / QuickTime ─► decodificação H.264 / PCM ─┐
  └─ AirPlay ─► WirelessHost ─► I420 / PCM ─┤
                                              └─► sessão nativa
                                                   ├─► prévias D3D11
                                                   ├─► captura de tela / OBS
                                                   ├─► FFmpeg MP4 / RTMP / SRT / WHIP
                                                   ├─► câmera virtual do Windows 11
                                                   └─► áudio WASAPI
```

Consulte a documentação de [protocolo](docs/PROTOCOL.md), [arquitetura](docs/ARCHITECTURE.md),
[renderização D3D11](docs/D3D11_RENDERING.md),
[perfis de canto dos dispositivos](docs/DEVICE_CORNER_PROFILES.md) e
[áudio WASAPI](docs/WASAPI_AUDIO.md). O trabalho planejado está no
[roteiro de evolução](docs/ROADMAP.md); itens do roteiro não são recursos implementados.

## Limitações atuais

- Gravação embutida e saída RTMP, SRT e WebRTC/WHIP incluem o áudio de origem quando a faixa de áudio e o codificador necessário estão disponíveis; caso contrário, iniciam imediatamente como saída somente de vídeo; MP4, RTMP e SRT codificam AAC, enquanto WHIP codifica Opus.
- O app não possui assinatura comercial de código.
- A matriz de instalação de drivers externos precisa de testes mais amplos.
- A Apple não publica o Screen Capture como API estável para terceiros.
- A compatibilidade AirPlay é não oficial e pode mudar com futuras versões do iOS.
- O controle Bluetooth depende do modo periférico BLE e do AssistiveTouch do iOS, e limita-se a operações de toque único estilo ponteiro.
- A disponibilidade de decodificação por hardware depende do MFT do Windows e do driver da GPU; sistemas sem suporte usam automaticamente decodificação por software, mantendo a prévia em D3D11.
- Saídas SDR são marcadas como BT.709 de faixa completa; apresentação HDR ainda depende da origem, do monitor e do cliente receptor.

## Contribuição e segurança

Leia o [SUPPORT.md](SUPPORT.md) antes de abrir uma issue e o
[CONTRIBUTING.md](CONTRIBUTING.md) antes de enviar um pull request. Relate
problemas de segurança por
[relato privado de vulnerabilidade](https://github.com/RayrenSX/iPhoneMirror/security/advisories/new).
Nunca publique UDID real, registro de pareamento ou captura USB sem redação.

## Licença e agradecimentos

O código original do iPhoneMirror é licenciado sob a
[GNU General Public License v3.0 only](LICENSE). A distribuição de versões modificadas ou
derivadas deve seguir os requisitos de disponibilidade de código-fonte, avisos e copyleft da GPLv3.
Componentes de terceiros incluídos permanecem sob suas próprias licenças;
consulte [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

O receptor sem fio é distribuído como um processo GPLv3 independente. Links exatos de
código-fonte, hashes binários e licenças de componentes estão incluídos em
`Wireless/licenses` em cada pacote de lançamento.

Referências de pesquisa de protocolo:

- [danielpaulus/quicktime_video_hack](https://github.com/danielpaulus/quicktime_video_hack)
- [chotgpt/quicktime_video_hack_windows](https://github.com/chotgpt/quicktime_video_hack_windows)

Apple, iPhone, iOS e QuickTime são marcas da Apple Inc. Este projeto não é
afiliado, patrocinado ou endossado pela Apple Inc.
