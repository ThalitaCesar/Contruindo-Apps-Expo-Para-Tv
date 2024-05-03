# Construindo Aplicativos Expo para TV

Esse artigo é baseado no artigo do expo sobre o desenvolvimento para TV, que é possível acessar [aqui](https://docs.expo.dev/guides/building-for-tv/) . 

* Para construir aplicativos Expo para TV (Android TV ou Apple TV), é necessário seguir algumas etapas. 
* O desenvolvimento de TV com Expo está disponível apenas a partir do SDK 50 e é considerado experimental, portanto nem todos os recursos e módulos do Expo estão disponíveis para TV.
* Usando a biblioteca React Native TV ela se torna capaz de alcançar dispositivos móveis e TV.
* A TV também funciona com react-navigation, react-native-video e muitas outras bibliotecas React Native de terceiros comumente usadas.
* Certos pacotes Expo não são suportados no momento, principalmente: Expo DevClient e Roteador Expo
* Aqui está o repositório da biblioteca [react-native-tvos](https://github.com/react-native-tvos/react-native-tvos) e sua documentação.
* Aqui está um projeto TV realizado com o expo e react-native-tvos, chamado [IgniteTV](https://github.com/react-native-tvos/IgniteTV)
* Aqui temos o arquivo de [config-tv](https://github.com/react-native-tvos/config-tv/tree/main/packages/config-tv) com as explicações sobre as configuração.

  ### O que foi mudado com o config-tv:
  
 #### Android:
 * AndroidManifest.xml foi modificado:
    * A orientação retrato padrão do telefone foi removida
    * A intenção necessária para aplicativos de TV foi adicionada
  * MainApplication.kt foi modificado para remover invocações Flipper não suportadas

  #### iOS:
  * ios/Podfile foi modificado para direcionar tvOS em vez de iOS
  * O projeto Xcode foi modificado para direcionar tvOS em vez de iOS
  * A tela inicial (SplashScreen.storyboard) foi modificada para funcionar no tvOS

A maneira mais rápida de gerar um novo projeto é descrita no Exemplo de TV no repositório de exemplos da Expo.
##### O React Native TV é suportado em Android TV e Apple TV (como também em Android e IOS)

## Passos para Integrar TV ao Projeto Expo

### Algumas configurações:
  * Um projeto Expo usando SDK 50 ou posterior.
  #### Android TV:
  * No gerenciador do Android Studio SDK, selecione o menu suspenso do Android SDK que você está usando (API versão 31 ou posterior) ecertifique-se de que uma imagem do sistema Android TV esteja selecionada para instalação. (Para silício Apple, escolha a imagem ARM 64. Caso contrário, escolha a imagem Intel x86_64).
  * Depois de instalar a imagem do sistema Android TV, crie um emulador de Android TV usando essa imagem (o processo é o mesmo que criar um emulador de telefone Android).

  #### Apple TV:
  * Xcode 15 ou posterior.
  * tvOS SDK 17 ou posterior. (Isso não é instalado automaticamente com o Xcode. Você pode in

### 1. Modifique as Dependências para TV

No arquivo `package.json`:

```json
{
  "dependencies": {
    "react-native": "npm:react-native-tvos"
  },
  "expo": {
    "install": {
      "exclude": [
        "react-native"
      ]
    }
  }
}
```


### 2. Adicione o Plugin de Configuração para TV


Execute o seguinte comando para instalar o plugin de configuração para TV:


```bash
npx expo install --dev @react-native-tvos/config-tv
```


Verifique se o plugin está listado corretamente em `app.json`:


```json
{
  "plugins": [
    "@react-native-tvos/config-tv"
  ]
}
```


### 3. Execute o Prebuild para Modificações de TV


Defina a variável de ambiente `EXPO_TV` como `1` e execute o prebuild para aplicar as modificações necessárias para suportar TV:


```bash
export EXPO_TV=1
npx expo prebuild --clean
```


### 4. Construa para Android TV


Inicie um emulador de Android TV e use o seguinte comando para iniciar o aplicativo no emulador:


```bash
npx expo run:android
```

### 5. Construa para Apple TV


Execute o seguinte comando para construir e executar o aplicativo em um simulador de Apple TV:


```bash
npx expo run:ios
```


### 6. Reverta as Modificações para TV e Construa para Dispositivo Móvel

Se desejar reverter as modificações para TV e voltar ao desenvolvimento para dispositivos móveis, desative a variável `EXPO_TV` e execute o prebuild novamente:


```bash
unset EXPO_TV
npx expo prebuild --clean
```

### 7. Crie perfis EAS Build para TV e telefone

Como a construção da TV é orientada pelo valor de uma variável de ambiente, é fácil configurar perfis de construção EAS que são construídos a partir da mesma fonte, mas têm como alvo a TV em vez do telefone.

O exemplo a seguir eas.json mostra como estender perfis existentes (desenvolvimento e visualização) para criar perfis de TV (development_tv e preview_tv).

```json
{
  "cli": {
    "version": ">= 5.2.0"
  },
  "build": {
    "base": {
      "distribution": "internal",
      "ios": {
        "simulator": true
      },
      "android": {
        "buildType": "apk",
        "withoutCredentials": true
      },
      "channel": "base"
    },
    "development": {
      "extends": "base",
      "android": {
        "gradleCommand": ":app:assembleDebug"
      },
      "ios": {
        "buildConfiguration": "Debug"
      },
      "channel": "development"
    },
    "development_tv": {
      "extends": "development",
      "env": {
        "EXPO_TV": "1"
      },
      "channel": "development"
    },
    "preview": {
      "extends": "base",
      "channel": "preview"
    },
    "preview_tv": {
      "extends": "preview",
      "env": {
        "EXPO_TV": "1"
      },
      "channel": "preview"
    }
  },
  "submit": {}
}


```




