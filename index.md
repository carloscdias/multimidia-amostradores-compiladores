<script type="text/javascript" src="js/csound.js"></script>
<script>
  // called by csound.js
  function moduleDidLoad() {
    console.log("Module loaded!");
  }

  function attachListeners() {
    document.getElementById("compile").
    addEventListener("click", compileAndRun);
    document.getElementById("csound_code").
    addEventListener("change", reset);
  }

  var count = 0;

  function handleMessage(message) {
    var element = document.getElementById('console');
    element.value += message;
    element.scrollTop = 99999; // focus on bottom
    count += 1;
    if (count == 1000) {
      element.value = ' ';
      count = 0;
    }
  }

  var started = false;

  function reset() {
    csound.Csound.stop();
    csound.Csound.reset();
    started = false;
  }

  function play() {
    let code = document.getElementById("csound_code").value;
    csound.Csound.compileCSD(code);
    csound.Csound.start();
    started = true;
  }

  // click handler
  function compileAndRun() {
    if (started)
      reset();
    play();
  }
</script>

<style>
#console {
  font-family: Monospace;
  color: #b5b6ff;
  background-color: #000000;
  font-size: 16px;
  width: 620px;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-style: solid;
  padding: 20px 0px;
  margin: 20px 0;
}

#csound_code {
  font-family: Monospace;
  font-size: 16px;
  width: 620px;
  height: 700px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-style: solid;
  padding: 5px 0px;
  margin: 20px 0;
}

#compile {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 150px;
  height: 30px;
  font-size: 16px;
  margin: auto;
}
</style>

## Amostradores de áudio

Amostradores de áudio, ou samplers, são sintetizadores de áudio PCM que utilizam uma memória RAM
para armazenamento dos áudios ao invés da memória ROM, como é utilizada na síntese PCM padrão.
Isso permite que o usuário faça as suas próprias definições para o formato de onda através da captura
de um som arbitrário.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ERy-99vXxnM?start=55" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Este equipamento registra amostras, ou samples, digitais de um som para serem usadas
posteriormente. O sampler é capaz de gravar digitalmente um som qualquer, editá-lo
e armazená-lo na memória para ser executado posteriormente através de um teclado
ou um sequenciador MIDI.

Para gravar um som no sampler, um microfone, ou outra fonte de sinal, é conectado à entrada de áudio
e se inicia o processo de digitalização, que é efetuado pelo conversor analógico/digital do sampler.
O som já digitalizado pelo ADC ("*A*nalog to *D*igital *C*onverter") é armazenado na memória
interna do sampler, onde é devidamente processado e configurado para ser uma forma-de-onda (“waveform”)
utilizável na edição de timbres, a partir daí é realizado um processo igual ao dos sintetizadores
digitais convencionais que utilizam síntese subtrativa para obter o resultado final,
um timbre que pode ser controlado por um teclado ou por um seqüenciador MIDI.

![Processo de entrada de áudio no sampler](/img/sampler.png)

A principal diferença do sampler para um sintetizador comum é que as formas-de-onda não
estão fixas na memória ROM, e podem ser carregadas pelo usuário. Isso faz do sampler um
equipamento dependente da qualidade das amostras que serão sampleadas pelo próprio usuário
ou carregadas a partir de mídia externa criadas por empresas especializadas.
O sampler dá ao compositor uma maior flexibilidade, já que os timbres podem ser trocados
desde a fonte, diferentemente de um sintetizador que possui formas-de-onda fixas para sempre.

Um dos trade-offs para um bom equipamento de sampling é o tamanho da memória disponível
e seu custo. Um equipamento com pouca memória para alocação de sons é, em geral, mais
barato, todavia, isto pode comprometer a qualidade do áudio, visto que com poucos
sons disponíveis na memória RAM do dispositivo é necessário um processamento interno
para atingir notas não armazenadas, este processamento não terá as mesmas características
do som original, caso a nota fosse executada no instrumento musical físico e armazenada
diretamente. Este comportamento faz com que um sampler com uma grande quantidade
de memória RAM disponível seja ideal, mas o custo do equipamento também aumenta.

## Compiladores acústicos

Compiladores acústicos são sintetizadores de áudio em tempo não-real que
permite que sons sejam programados de maneira análoga à uma linguagem de
programação. Este mecanismo dá maior controle ao compositor, uma vez que, a síntese
é realizada pelo próprio microprocessador central do computador e permite que
algoritmos arbitrários sejam programados.

As linguagens de síntese musical mais difundidas são CMusic e CSound, mas existem
muitas outras como o SuperCollider e ChucK, por exemplo. Estas linguagens permitem
a definição de instrumentos virtuais que executam partituras formadas por listas de notas,
cada uma com o seu tempo de início, duração, intensidade e timbre. Estas composições
são realizadas em blocos, semelhantes às estruturas de linguagens de programação.

Existem hoje sistemas mais eficientes de compiladores acústicos como, por exemplo,
Accelerando, Kyma e IRCAM Musical Workstation, mas que são dedicados a hardware
específico. Estes sistemas incluem interface gráfica que possibilita uma 
interação mais intuitiva na especificação e edição de tabelas de onda, envelopes
e espectro harmônico, além de prover também recursos de controle em tempo real,
mesmo que limitado.

Você pode ser umx programadorx DJ abaixo editando, compilando e gerando seu próprio som
com CSound nesta aplicação especialmente desenvolvida para a disciplina. Caso esteja
sem criatividade, teste algum som disponível [na biblioteca de exemplos](http://www.csounds.com/manual/html/MiscExamples.html).

**\*obs:** Devido à política do chrome de bloqueio de áudio, recomenda-se a utilização do editor no firefox

<textarea id="csound_code" name="csound_code">
<CsoundSynthesizer>
<CsOptions>
; Select audio/midi flags here according to platform
-odac     ;;;RT audio out
;-iadc    ;;;uncomment -iadc if RT audio input is needed too
; For Non-realtime ouput leave only the line below:
; -o buzz.wav -W ;;; for file output any platform
</CsOptions>
<CsInstruments>

sr = 44100
ksmps = 32
nchnls = 2
0dbfs = 1

instr 1

kcps = 110
ifn  = 1

knh	line p4, p3, p5
asig	buzz 1, kcps, knh, ifn
	outs asig, asig
endin

</CsInstruments>
<CsScore>

;sine wave.
f 1 0 16384 10 1

i 1 0 3 20 20
i 1 + 3 3 3
i 1 + 3 10 1
e

</CsScore>
</CsoundSynthesizer>
</textarea>
<textarea id="console" readonly name="console"></textarea>
<button id="compile">Compilar e tocar</button>

### Fontes
 - Manual de Estudio (Disponibilizado na página da disciplina no moodle)
 - https://antigo.anppom.com.br/anais/anaiscongresso_anppom_1995/comppairelat3.htm
 - https://csound.com/
 - Notas de aula
