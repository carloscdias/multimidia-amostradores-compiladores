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
    width: 805px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-style: solid;
    padding: 20px 0px;
}

#csound_code {
    font-family: Monospace;
    font-size: 16px;
    width: 805px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-style: solid;
    padding: 20px 0px;
}
</style>

# Técnicas de síntese digital de som: amostradores e compiladores acústicos

## Amostradores de áudio

Amostradores de áudio, ou samplers, são sintetizadores de áudio PCM que utilizam uma memória RAM
para armazenamento dos áudios ao invés da memória ROM, como é utilizada na síntese PCM.
Isso permite que o usuário faça as suas próprias definições para o formato de onda.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ERy-99vXxnM?start=55" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Este equipamento registra amostras, ou samples, digitais de um som para serem usadas
posteriormente. O sampler é capaz de gravar digitalmente um som qualquer, editá-lo
e armazená-lo na memória para ser executado posteriormente através de um teclado
ou um sequenciador MIDI.

  <!-- <p>
    Para gravar um som no sampler, basta conectar à sua entrada de áudio um microfone (ou
    uma outra fonte de sinal, como um toca-discos de CD, por exemplo) e iniciar o processo de
    digitalização, que é efetuado pelo conversor analógico/digital do sampler. Nesse momento, o
    som já digitalizado é armazenado então na memória interna do sampler, onde é devidamente
    processado e configurado para ser uma forma-de-onda (“waveform”) utilizável na edição de
    timbres, daí então um processo igual ao dos sintetizadores digitais convencionais que utilizam
    síntese subtrativa, que dará o resultado final, ou seja, um timbre que pode ser controlado por
    um teclado ou por um seqüenciador MIDI.
  </p> -->
  <!-- <p>
    A principal diferença do sampler para um sintetizador comum é que as formas-de-onda não
    estão fixas na memória, e podem ser carregadas pelo usuário. Isso faz do sampler um
    equipamento dependente da qualidade das amostras que nele serão colocadas (sampleadas
    pelo próprio usuário ou carregadas a partir de CD-ROMs criados por empresas especializadas).
    O sampler é, portanto, um instrumento extremamente flexível e “atualizável”, pois a parte
    principal - os timbres - podem ser trocados desde a fonte, diferentemente de um sintetizador
    que possui formas-de-onda fixas, para sempre.
  </p> -->
  <!-- <p>
    Estando a amostra na memória, é necessário ajustá-la adequadamente para que possa ser
    usada. Um dos aspectos mais importantes a se entender no processo de sampling é quanto à
    faixa utilizável de uma amostra. Todo som natural (voz humana, violino, etc) possui uma
    característica chamada de “formante”, que delineia as amplitudes dos harmônicos presentes
    em seu espectro de freqüências, conforme um padrão próprio. Essa formante está diretamente
    associada ao dispositivo físico onde o som é gerado (corpo do violino, estrutura da laringe,
    etc). Isso faz com que mesmo notas de alturas diferentes tenham um contorno bastante
    similar nas amplitudes do espectro.
  </p> -->
  <!-- <p>
    Na a seguir podemos observar o que foi descrito acima. O diagrama A mostra o espectro de
    freqüências produzido pela nota Lá 3 de um violino. A formante (representada pela linha
    vermelha), nesse caso, mostra algumas ênfases, principalmente nas proximidades das
    freqüências de 4.300, 8.700 e 12.000 Hz. O diagrama B mostra o espectro de freqüências
    produzido pela nota Lá 4 do mesmo violino. Observe que sua formante mostra praticamente as
    mesmas ênfases da nota Lá 3. Este exemplo deixa claro que, nos sons naturais, as
    características de amplitude do espectro são fixas, de acordo com uma formante própria, não
    se alterando com a altura do som.
  </p> -->
  <!-- <p>
    Se tentarmos usar a nota Lá 3 para gerar a nota Lá 4, efetuando uma transposição por
    processamento digital (dobrando a freqüência por meio de um recurso simples de “pitch
    shifting”), teremos uma nota Lá 4 com um espectro de freqüências obedecendo a uma
    formante completamente diferente da original daquele violino (diagrama C). Na prática, isso
    fará com que o som dessa nota Lá 4 soe estranho (ainda que com características de violino).
    Isso é bastante perceptível quando se aumenta a velocidade de um gravador de fita (será que
    alguém ainda tem um?) ou aumentando a sampling rate na reprodução do som digital. Se a
    gravação original contém voz, por exemplo, o resultado será bem engraçado, com a
    característica de “voz de pato”.
  </p> -->
  <!-- <p>
    Além da deterioração da formante, podem ocorrer também outros problemas ao se transpor
    uma amostra. O efeito de vibrato, por exemplo, que se caracteriza por uma leve modulação na
    freqüência do som, da ordem de 5 Hz, pode se tornar irritante ao se transpor a amostra para o
    dobro da freqüência. Outros efeitos, como os ataques de metais, o ruído de sopro de uma
    flauta, e mesmo a reverberação acústica presente na amostra, podem ser totalmente
    descaracterizados após uma transposição acentuada.
  </p> -->
  <!-- <p>
    Por isso, para se samplear um som natural, é necessário fazer várias amostragens, 
    evitandose ao máximo o aproveitamento da amostra de uma nota para as demais. Esse processo é
    chamado de “multisampling”, e associa uma amostra a um pequeno grupo de notas (veja
    figura a seguir). Idealmente, cada nota deveria ter sua própria amostra, mas como a
    quantidade de amostras está diretamente associada à capacidade de armazenamento de
    memória, que custa caro, os fabricantes têm que encontrar uma relação viável entre o custo
    (número de amostras) e a qualidade, para poder colocar seu equipamento no mercado.
  </p> -->
  <!-- <p>
    Existem algoritmos sofisticados de pitch-shifting que permitem fazer grandes alterações da
    freqüência da amostra sem que haja uma deterioração perceptível da formante. Um exemplo
    disso é a tecnologia Variphrase da Roland. A aplicação mais evidente do processo de
    multisampling são os kits de bateria e percussão, onde cada tecla (nota) está associada a um
    único timbre, composto por sua própria amostra.
  </p> -->
## Compiladores acústicos

Compiladores acústicos são sintetizadores de áudio em tempo não-real que
permite que sons sejam programados de maneira análoga à uma linguagem de
programação. Este mecanismo dá maior liberdade ao compositor, uma vez que, a síntese
é realizada pelo próprio microprocessador central do computador e permite que
algoritmos arbitrários sejam programados.

As linguagens de síntese musical mais difundidas são CMusic e CSound. Estas 
linguagens permitem a definição de instrumentos virtuais que executam partituras
formadas por listas de notas, cada uma com o seu tempo de início, duração, intensidade e
timbre. Estas composições são realizadas em blocos, semelhantes às estruturas de
linguagens de programação.

Seja um programador DJ abaixo editando, compilando e gerando seu próprio som
com CSound.

<textarea id="csound_code" name="csound_code" cols="200" rows="7">
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
<textarea id="console" readonly name="console" cols="30" rows="7"></textarea>
<button id="compile">Compilar e tocar</button>

\*obs: devido à política do chrome de bloqueio de áudio, recomenda-se a utilização do editor no firefox

### Fontes
 - Manual de Estudio (Disponibilizado na página da disciplina no moodle)
 - https://antigo.anppom.com.br/anais/anaiscongresso_anppom_1995/comppairelat3.htm
 - https://csound.com/
