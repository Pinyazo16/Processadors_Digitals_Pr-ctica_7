# Processadors_Digitals_Practica_7
# Practica 7

Aquesta pràctica consistia en utilitzar els busos I2S per la transmisió i reproducció d’audio. Aquest bus està especificament dissenyat per la transmisió de senyals d’audio digital, i requereix de tres cables per al seu funcionament.

## Exercici 1

Aquest primer exercici consisteix en reproduir un fitxer de so emmagatzemat a la memòria interna de la ESP32 mitjançant el bus I2S per connectar la placa amb els altaveus. Per aquest exercici utilitzarem la llibreria ESP8266 de Earle F. Philhower; de la qual utilitzarem un dels fitxers d’exemple com arxiu a reproduir.

### Funció `setup()`

En aquesta funció iniciarem el bus I2S amb l’ajuda de funcions de la llibreria ESP8266 mencionanda anteriorment.

```cpp
AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;

void setup(){
  Serial.begin(115200);
  in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
  aac = new AudioGeneratorAAC();
  out = new AudioOutputI2S();
  out -> SetGain(0.125);
  out -> SetPinout(26,25,22);
  aac->begin(in, out);
}
```

### Funció `loop()`

En aquesta segona funció crearem dos casos `if()` que ens serviràn per reproduir el fitxer de so que emmagatzemarem dins les capretes del projecte.

```cpp
void loop(){
  if (aac->isRunning()) {
    aac->loop();
  } else {
    aac -> stop();
    Serial.printf("Sound Generator\n");
    delay(1000);
  }
}
```

El primer `if()` detecta si s’està reproduint o no el fitxer d’audio. En cas afirmatiu, el fa reproduir-se en bucle. En cas contrari, es passa al segón `if()` (en aquest cas és un `else()` ) el qual fa parar la reproducció del fitxer, imprimeix per pantalla el nom del fitxer de so i introdueix un `delay()` d’un segón abans de tornar a comprobar el primer cas `if()` .

## Exercici 2

En aquest segón exercici connectarem a la ESP32, a més dels altaveus, un lector de targetes SD des del qual llegirem el fitxer d’audio que volem reproduir. EN aquestexercici també farem servir una llibreria externa; en aquest cas la llibreria d’audio I2S de schreibaful1; la qual està dissenyada específicament per realitzar el mateix procés que volem recrear en aquesta pràctica.

### Funció `setup()`

En aquesta funció, i de la mateixa forma que en l’anterior exercici, inicialitzarem la comunicació I2S i assignarem els pins corresponents per poder establir la comunicació amb la targeta SD.

 

```cpp
void setup(){
  pinMode(SD_CS, OUTPUT);
  digitalWrite(SD_CS, HIGH);
  SPI.begin(SPI_SCK, SPI_MISO, SPI_MOSI);
  Serial.begin(115200);
  SD.begin(SD_CS);
  audio.setPinout(I2S_BCLK, I2S_LRC, I2S_DOUT);
  audio.setVolume(10); // 0...21
  audio.connecttoFS(SD, "nina.wav"); //nina.wav
  time1= millis();
}
```

Aqui podem veure l’esquema de connexions i l’assignació de pins utilitzats per a aquest exercici.

```cpp
#define SD_CS 5
#define SPI_MOSI 23
#define SPI_MISO 19
#define SPI_SCK 18
#define I2S_DOUT 25
#define I2S_BCLK 27
#define I2S_LRC 26
```

![Untitled](Practica%207%2090eec507a7ef4783a331dd89c57f6335/Untitled.png)

### Funció `loop()`

Aquesta funció conté únicament la funció `audio.loop()` de la llibreria que hem comentat anteriorment.

### Treball extra

Coma treball extra hem afegit el codi necessari a la funció `loop()` per que aturi la reproducció del fitxer d’audio passat un cert temps.

```cpp
unsigned long int time1;
unsigned long int time2;

void loop(){
  audio.loop();
  time2=millis();
  if( audio.isRunning()==true && time2>=time1+10000){
    audio.stopSong();
  }
}
```

Com es pot veure, tornem a fer ús de la llibreria per aturar la reproducció del fitxer amb la funció `audio.stopSong()` passats 10 segons d’iniciar la reproducció.
