---
title: Intento Android
aliases:
  - Intent Android
  - intento implícito
  - intento explícito
  - resolução de intento
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Intento Android

## Definição

Mecanismo central do Android para descoberta e ativação de componentes de aplicação (Activities, Services, BroadcastReceivers). Permite que componentes interajam sem se conhecerem diretamente.

## Papel técnico

Análogo ao caminho de busca do shell do Linux: o sistema encontra o componente adequado para executar uma ação descrita, sem que o chamador saiba qual implementação será usada.

## Relações (SPO)

- Intento -> ativa -> Activity, Service ou BroadcastReceiver
- Intento explícito -> identifica -> componente pelo nome exato
- Intento implícito -> descreve -> ação desejada (sem nomear componente)
- Resolução de intento -> encontra -> componente que declara `<intent-filter>` compatível
- Gerenciador de pacotes -> retorna -> implementações candidatas

## Tipos de Intento

### Intento Explícito

Identifica diretamente um componente por **package name** + **class name**:

```java
Intent intent = new Intent();
intent.setClassName("com.example.email", 
                    "com.example.email.MailMainActivity");
startActivity(intent);
```

Análogo a: fornecer caminho absoluto para um comando shell.

### Intento Implícito

Descreve características da ação desejada; o sistema encontra quem pode realizá-la:

```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("image/jpeg");
startActivity(Intent.createChooser(intent, "Compartilhar"));
```

Análogo a: fornecer apenas o nome do comando ao shell, que busca no `PATH`.

## Filtros de Intento

Apps declaram no manifesto (`AndroidManifest.xml`) quais intentos podem tratar:

```xml
<activity android:name=".ComposeActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <data android:mimeType="*/*" />
    </intent-filter>
</activity>
```

## Resolução de Intento

3 possíveis resultados:
1. **Nenhum pareamento**: erro ou dialog de erro
2. **Um único pareamento**: componente lançado diretamente
3. **Múltiplos pareamentos**: seletor (chooser) exibido ao usuário

## Compartilhamento — Caso de Uso

App de câmera quer compartilhar foto:
1. Cria intento com `android.intent.action.SEND` + MIME type `image/jpeg`
2. Sistema encontra todos os apps com filtro compatível (email, WhatsApp, Drive...)
3. Usuário seleciona; sistema lança o componente escolhido

## Ver também

- [[Android]]
- [[Binder]]
- [[Segurança no Linux]]
