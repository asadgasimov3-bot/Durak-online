from pathlib import Path
import zipfile, textwrap, json, shutil, os

base = Path("/mnt/data/DurakOnline")
if base.exists():
    shutil.rmtree(base)
(base/"src"/"components").mkdir(parents=True)
(base/"src"/"screens").mkdir(parents=True)
(base/"src"/"game").mkdir(parents=True)
(base/"src"/"theme").mkdir(parents=True)

files = {
"package.json": """{
  "name": "durak-online",
  "version": "1.0.0",
  "private": true,
  "main": "expo-router/entry",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "web": "expo start --web",
    "typecheck": "tsc --noEmit"
  },
  "dependencies": {
    "expo": "~54.0.0",
    "expo-router": "~6.0.0",
    "expo-status-bar": "~3.0.0",
    "react": "19.1.0",
    "react-native": "0.81.4",
    "react-native-safe-area-context": "~5.6.0",
    "react-native-screens": "~4.16.0"
  },
  "devDependencies": {
    "@types/react": "~19.1.10",
    "typescript": "~5.9.2"
  }
}""",
"app.json": """{
  "expo": {
    "name": "Durak Online",
    "slug": "durak-online",
    "scheme": "durakonline",
    "version": "1.0.0",
    "orientation": "portrait",
    "userInterfaceStyle": "dark",
    "android": {
      "package": "com.durakonline.game",
      "versionCode": 1
    },
    "plugins": ["expo-router"]
  }
}""",
"tsconfig.json": """{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,
    "paths": { "@/*": ["./src/*"] }
  }
}""",
"babel.config.js": """module.exports = function(api) {
  api.cache(true);
  return { presets: ['babel-preset-expo'] };
};""",
"app/_layout.tsx": """import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';

export default function RootLayout() {
  return <>
    <StatusBar style="light" />
    <Stack screenOptions={{ headerShown: false, animation: 'fade' }} />
  </>;
}
""",
"app/index.tsx": """import { HomeScreen } from '../src/screens/HomeScreen';
export default HomeScreen;
""",
"app/lobby.tsx": """import { LobbyScreen } from '../src/screens/LobbyScreen';
export default LobbyScreen;
""",
"src/theme/colors.ts": """export const colors = {
  felt: '#073D2B',
  feltLight: '#14563D',
  feltDark: '#05291E',
  gold: '#E4BD70',
  goldDark: '#A77B35',
  cream: '#F7F0DF',
  red: '#C92F3C',
  ink: '#17211C',
  muted: '#B8C9BE',
  panel: '#0D4934',
  border: '#9A783F'
};
""",
"src/components/GoldButton.tsx": """import React from 'react';
import { Pressable, Text, StyleSheet, ViewStyle } from 'react-native';
import { colors } from '../theme/colors';

type Props = { title: string; onPress: () => void; secondary?: boolean; style?: ViewStyle };
export function GoldButton({ title, onPress, secondary, style }: Props) {
  return <Pressable onPress={onPress} style={({pressed}) => [
    styles.button, secondary && styles.secondary, pressed && styles.pressed, style
  ]}>
    <Text style={[styles.text, secondary && styles.secondaryText]}>{title}</Text>
  </Pressable>;
}
const styles = StyleSheet.create({
  button: { minHeight: 56, borderRadius: 15, borderWidth: 1, borderColor: '#F4D995',
    backgroundColor: colors.gold, justifyContent: 'center', alignItems: 'center',
    paddingHorizontal: 18, shadowColor: '#000', shadowOpacity: .25, shadowRadius: 7,
    shadowOffset: {width: 0, height: 4}, elevation: 5 },
  secondary: { backgroundColor: '#0B3829', borderColor: colors.goldDark },
  text: { color: '#241A0B', fontSize: 16, fontWeight: '900', letterSpacing: 1 },
  secondaryText: { color: colors.cream },
  pressed: { opacity: .82, transform: [{scale: .985}] }
});
""",
"src/components/PlayingCard.tsx": """import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';

export type Suit = '♠' | '♥' | '♦' | '♣';
export type Rank = '6'|'7'|'8'|'9'|'10'|'J'|'Q'|'K'|'A';
export type CardData = { id: string; suit: Suit; rank: Rank };

export function PlayingCard({ card, back = false }: {card?: CardData; back?: boolean}) {
  if (back) return <View style={[styles.card, styles.back]}>
    <View style={styles.backInner}><Text style={styles.backMark}>✦</Text></View>
  </View>;
  if (!card) return null;
  const red = card.suit === '♥' || card.suit === '♦';
  return <View style={styles.card}>
    <Text style={[styles.corner, red && styles.red]}>{card.rank}{'\\n'}{card.suit}</Text>
    <Text style={[styles.center, red && styles.red]}>{card.suit}</Text>
    <Text style={[styles.bottom, red && styles.red]}>{card.rank}</Text>
  </View>;
}
const styles = StyleSheet.create({
  card: { width: 61, height: 91, borderRadius: 8, backgroundColor: '#FFFDF6',
    borderWidth: 1, borderColor: '#D8D0BE', padding: 5, justifyContent: 'space-between',
    shadowColor: '#000', shadowOpacity: .28, shadowRadius: 4, elevation: 4 },
  corner: { color: colors.ink, fontWeight: '900', fontSize: 13, lineHeight: 14 },
  center: { alignSelf: 'center', fontSize: 34, color: colors.ink },
  bottom: { alignSelf: 'flex-end', fontSize: 12, fontWeight: '900', color: colors.ink },
  red: { color: colors.red },
  back: { backgroundColor: '#123F32', borderColor: colors.gold, padding: 5 },
  backInner: { flex: 1, borderWidth: 1, borderColor: colors.gold, borderRadius: 5,
    justifyContent: 'center', alignItems: 'center' },
  backMark: { color: colors.gold, fontSize: 27 }
});
""",
"src/game/deck.ts": """import type { CardData, Suit, Rank } from '../components/PlayingCard';

const suits: Suit[] = ['♠', '♥', '♦', '♣'];
const ranks: Rank[] = ['6','7','8','9','10','J','Q','K','A'];

export function createDeck(): CardData[] {
  return suits.flatMap(suit => ranks.map(rank => ({
    id: `${rank}${suit}`, suit, rank
  })));
}
export function shuffle<T>(items: T[]): T[] {
  const result = [...items];
  for (let i = result.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [result[i], result[j]] = [result[j], result[i]];
  }
  return result;
}
""",
"src/screens/HomeScreen.tsx": """import React from 'react';
import { View, Text, StyleSheet, ScrollView, Pressable } from 'react-native';
import { useRouter } from 'expo-router';
import { colors } from '../theme/colors';
import { GoldButton } from '../components/GoldButton';
import { PlayingCard } from '../components/PlayingCard';

export function HomeScreen() {
  const router = useRouter();
  return <ScrollView contentContainerStyle={styles.page}>
    <View style={styles.top}>
      <View style={styles.avatar}><Text style={styles.avatarText}>♟</Text></View>
      <View style={{flex:1}}>
        <Text style={styles.player}>Игрок</Text>
        <Text style={styles.level}>★ Уровень 1</Text>
      </View>
      <View style={styles.online}><View style={styles.dot}/><Text style={styles.onlineText}>Онлайн</Text></View>
      <Pressable accessibilityLabel="Настройки" style={styles.settings}><Text style={styles.gear}>⚙</Text></Pressable>
    </View>

    <View style={styles.hero}>
      <View style={styles.cards}>
        <View style={{transform:[{rotate:'-12deg'}]}}><PlayingCard card={{id:'K♦',rank:'K',suit:'♦'}}/></View>
        <View style={{transform:[{translateY:-13}]}}><PlayingCard card={{id:'Q♠',rank:'Q',suit:'♠'}}/></View>
        <View style={{transform:[{rotate:'12deg'}]}}><PlayingCard card={{id:'A♥',rank:'A',suit:'♥'}}/></View>
      </View>
      <Text style={styles.logo}>DURAK</Text>
      <Text style={styles.onlineLogo}>ONLINE</Text>
      <Text style={styles.subtitle}>Переводной дурак</Text>
    </View>

    <View style={styles.buttons}>
      <GoldButton title="▶  ИГРАТЬ" onPress={() => router.push('/lobby')} />
      <GoldButton title="♟  СОЗДАТЬ КОМНАТУ" secondary onPress={() => router.push('/lobby')} />
      <GoldButton title="↪  ВОЙТИ ПО КОДУ" secondary onPress={() => router.push('/lobby')} />
    </View>

    <View style={styles.shortcuts}>
      {[
        ['🏆','Рейтинг'],['▥','Статистика'],['?','Правила'],['↗','Пригласить']
      ].map(([symbol,label]) => <Pressable key={label} style={styles.shortcut}>
        <View style={styles.shortcutCircle}><Text style={styles.shortcutIcon}>{symbol}</Text></View>
        <Text style={styles.shortcutLabel}>{label}</Text>
      </Pressable>)}
    </View>
    <Text style={styles.footer}>36 КАРТ  •  2 ИГРОКА</Text>
  </ScrollView>;
}
const styles = StyleSheet.create({
  page:{flexGrow:1,backgroundColor:colors.felt,paddingHorizontal:20,paddingTop:24,paddingBottom:30},
  top:{flexDirection:'row',alignItems:'center',gap:10},
  avatar:{width:48,height:48,borderRadius:24,borderWidth:2,borderColor:colors.gold,alignItems:'center',justifyContent:'center'},
  avatarText:{fontSize:25,color:colors.cream},
  player:{color:colors.cream,fontSize:16,fontWeight:'800'},
  level:{color:colors.gold,fontSize:12,marginTop:3},
  online:{flexDirection:'row',alignItems:'center',gap:6,borderColor:colors.goldDark,borderWidth:1,borderRadius:20,paddingVertical:8,paddingHorizontal:12},
  dot:{width:8,height:8,borderRadius:4,backgroundColor:'#54E27C'},
  onlineText:{color:colors.cream,fontWeight:'700',fontSize:12},
  settings:{width:42,height:42,borderRadius:22,borderWidth:1,borderColor:colors.gold,alignItems:'center',justifyContent:'center'},
  gear:{color:colors.gold,fontSize:24},
  hero:{alignItems:'center',marginTop:46,marginBottom:30},
  cards:{height:115,flexDirection:'row',alignItems:'center',justifyContent:'center',gap:3},
  logo:{color:colors.gold,fontSize:49,fontWeight:'900',letterSpacing:2,textShadowColor:'#000',textShadowRadius:8},
  onlineLogo:{color:'#F6E7C4',fontSize:20,fontWeight:'900',letterSpacing:9,marginTop:-3},
  subtitle:{color:colors.gold,fontSize:20,fontWeight:'700',marginTop:18},
  buttons:{gap:12},
  shortcuts:{flexDirection:'row',justifyContent:'space-between',marginTop:34},
  shortcut:{alignItems:'center',gap:7,flex:1},
  shortcutCircle:{width:52,height:52,borderRadius:28,borderWidth:1.5,borderColor:colors.goldDark,alignItems:'center',justifyContent:'center',backgroundColor:colors.feltDark},
  shortcutIcon:{fontSize:23,color:colors.gold},
  shortcutLabel:{color:colors.cream,fontSize:11,fontWeight:'600'},
  footer:{color:colors.gold,textAlign:'center',marginTop:35,letterSpacing:3,fontWeight:'800'}
});
""",
"src/screens/LobbyScreen.tsx": """import React, {useState} from 'react';
import { View, Text, TextInput, StyleSheet, Alert, Pressable } from 'react-native';
import { useRouter } from 'expo-router';
import { colors } from '../theme/colors';
import { GoldButton } from '../components/GoldButton';

export function LobbyScreen() {
  const router=useRouter();
  const [name,setName]=useState('Игрок');
  const [code,setCode]=useState('');
  const [created,setCreated]=useState('');
  const makeCode=()=>String(Math.floor(100000+Math.random()*900000));
  return <View style={styles.page}>
    <Pressable onPress={()=>router.back()}><Text style={styles.back}>‹  Назад</Text></Pressable>
    <Text style={styles.title}>Онлайн-комната</Text>
    <Text style={styles.subtitle}>Играй с другом по коду приглашения</Text>
    <Text style={styles.label}>Твоё имя</Text>
    <TextInput value={name} onChangeText={setName} maxLength={18} style={styles.input} placeholder="Имя игрока" placeholderTextColor="#9BA99F"/>
    <GoldButton title="СОЗДАТЬ КОМНАТУ" onPress={()=>setCreated(makeCode())}/>
    {created ? <View style={styles.codeBox}>
      <Text style={styles.label}>КОД ПРИГЛАШЕНИЯ</Text>
      <Text style={styles.code}>{created}</Text>
      <Text style={styles.note}>Демо-режим: подключение Supabase будет добавлено следующим этапом.</Text>
    </View>:null}
    <View style={styles.separator}/>
    <Text style={styles.label}>Код друга</Text>
    <TextInput value={code} onChangeText={v=>setCode(v.replace(/\\D/g,'').slice(0,6))} keyboardType="number-pad" maxLength={6} style={styles.input} placeholder="000000" placeholderTextColor="#9BA99F"/>
    <GoldButton title="ВОЙТИ В КОМНАТУ" secondary onPress={()=>Alert.alert('Онлайн ещё не подключён','Сначала подключим Supabase, чтобы комнаты работали между телефонами.')}/>
  </View>;
}
const styles=StyleSheet.create({
 page:{flex:1,backgroundColor:colors.felt,padding:22,paddingTop:55,gap:14},
 back:{color:colors.gold,fontSize:17,fontWeight:'700'},
 title:{color:colors.cream,fontSize:29,fontWeight:'900',marginTop:18},
 subtitle:{color:colors.muted,marginBottom:20},
 label:{color:colors.gold,fontWeight:'800',fontSize:13,letterSpacing:1},
 input:{backgroundColor:'#0B3326',borderWidth:1,borderColor:colors.goldDark,borderRadius:12,padding:15,color:colors.cream,fontSize:18},
 codeBox:{backgroundColor:'#0B3326',borderWidth:1,borderColor:colors.gold,borderRadius:14,padding:18,alignItems:'center',gap:8},
 code:{color:colors.gold,fontSize:38,fontWeight:'900',letterSpacing:7},
 note:{color:colors.muted,fontSize:12,textAlign:'center'},
 separator:{height:1,backgroundColor:'#47735D',marginVertical:10}
});
""",
"README.md": """# Durak Online — Android starter

This is the first React Native / Expo project scaffold for the Durak Online app.

## Included
- Portrait Android app configuration
- Green felt + gold visual theme
- Main menu screen
- Lobby screen with local demo room-code generation
- Reusable playing-card component
- 36-card deck generator and shuffle utility

## Run locally
1. Install Node.js LTS.
2. Install dependencies: `npm install`
3. Start Expo: `npx expo start`
4. Open the project with Expo Go, or press `a` with an Android emulator connected.

## Build an Android APK
Install EAS CLI and sign in:
`npm install -g eas-cli`
`eas login`
`eas build:configure`
`eas build --platform android --profile preview`

Add an `eas.json` with an Android APK preview profile before building:
```json
{
  "build": {
    "preview": {
      "distribution": "internal",
      "android": { "buildType": "apk" }
    }
  }
}
```

## Important current limitations
The room code is generated locally for UI demonstration only. There is no Supabase connection, authentication, multiplayer synchronization, server-authoritative game engine, or secure hidden-hand handling yet. Do not use this prototype for real online matches.

## Next engineering steps
1. Add Supabase project URL and publishable key through Expo public environment variables.
2. Create rooms / players / game state schema with strict Row Level Security.
3. Implement server-authoritative actions (attack, beat, transfer, take, pass).
4. Publish only each player's permitted hand to that player; never expose opponent hand in client-readable state.
5. Subscribe to room updates using Supabase Realtime.
6. Add automated rules tests and reconnect handling.
"""
}
for rel, content in files.items():
    p=base/rel
    p.parent.mkdir(parents=True, exist_ok=True)
    p.write_text(content, encoding="utf-8")

zip_path=Path("/mnt/data/DurakOnline_Android_Starter.zip")
with zipfile.ZipFile(zip_path,"w",zipfile.ZIP_DEFLATED) as z:
    for p in base.rglob("*"):
        if p.is_file():
            z.write(p,p.relative_to(base.parent))
print(f"Created {zip_path} ({zip_path.stat().st_size:,} bytes) with {len(files)} files.")
