<template>
    <form>
        <div>
            <!-- Barra de progresso -->
            <div v-if="step < numberSteps" class="progress-bar-container">
                <div class="progress-bar" :style="{ width: progress + '%' }"></div>
                <div class="progress-steps">
                    <div v-for="n in (numberSteps - 1)" :key="n" class="progress-step" :class="{ 'active': step >= n }">
                    </div>
                </div>
            </div>

            <!-- Step 1: Gênero e idade -->
            <div v-if="step === 1">
                <h4>Qual o seu gênero?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': genero === 'masculino' }">
                        <input type="radio" value="masculino" v-model="genero">
                        Masculino
                    </label>
                    <label :class="{ 'checked': genero === 'feminino' }">
                        <input type="radio" value="feminino" v-model="genero">
                        Feminino
                    </label>
                </div>
                <h4>Qual a sua idade?</h4>
                <div class="input-container" id="input-idade">
                    <input type="number" required v-model="idade">
                </div>
                <div v-if="idade >= 16 && idade < 18">
                    <h4>Você tem autorização dos seus pais para doar sangue?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': autorizacao === 'sim' }">
                            <input type="radio" value="sim" v-model="autorizacao">
                            Sim
                        </label>
                        <label :class="{ 'checked': autorizacao === 'nao' }">
                            <input type="radio" value="nao" v-model="autorizacao">
                            Não
                        </label>
                    </div>
                </div>
            </div>

            <!-- Step 2: Peso e altura -->
            <div v-if="step === 2">
                <h4>Qual o seu peso?</h4>
                <div class="input-container" id="input-peso">
                    <input type="number" required v-model="peso">
                </div>
                <h4>Qual a sua altura?</h4>
                <div class="input-container" id="input-altura">
                    <input type="text" required :value="alturaFormatada" @input="formatarAltura($event.target.value)"
                        maxlength="4">
                </div>
                <div v-if="imc > 40 && imc <= 50">
                    <h4>Você tem uma avaliação médica recente com exames laboratoriais nos últimos 6 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': exame === 'sim' }">
                            <input type="radio" value="sim" v-model="exame">
                            Sim
                        </label>
                        <label :class="{ 'checked': exame === 'nao' }">
                            <input type="radio" value="nao" v-model="exame">
                            Não
                        </label>
                    </div>
                </div>
            </div>

            <!-- Step 3: Doação -->
            <div v-if="step === 3">
                <h4>Você já doou sangue antes?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': doador === 'sim' }">
                        <input type="radio" value="sim" v-model="doador">
                        Sim
                    </label>
                    <label :class="{ 'checked': doador === 'nao' }">
                        <input type="radio" value="nao" v-model="doador">
                        Não
                    </label>
                </div>
                <div v-if="doador === 'sim' && genero === 'feminino'">
                    <h4>Sua última doação foi nos últimos 3 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': doacaoMulherRecente === 'sim' }">
                            <input type="radio" value="sim" v-model="doacaoMulherRecente">
                            Sim
                        </label>
                        <label :class="{ 'checked': doacaoMulherRecente === 'nao' }">
                            <input type="radio" value="nao" v-model="doacaoMulherRecente">
                            Não
                        </label>
                    </div>
                </div>
                <div v-if="doador === 'sim' && genero === 'masculino'">
                    <h4>Sua última doação foi nos últimos 2 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': doacaoHomemRecente === 'sim' }">
                            <input type="radio" value="sim" v-model="doacaoHomemRecente">
                            Sim
                        </label>
                        <label :class="{ 'checked': doacaoHomemRecente === 'nao' }">
                            <input type="radio" value="nao" v-model="doacaoHomemRecente">
                            Não
                        </label>
                    </div>
                </div>
            </div>

            <!-- Step 4: Sintomas gerais -->
            <div v-if="step === 4">
                <h3>Sintomas gerais</h3>
                <h4>Você sentiu algum sintoma de doença como: febre, vômitos, tonturas, moleza no corpo... nos últimos
                    15 dias?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': sintomas === 'sim' }">
                        <input type="radio" value="sim" v-model="sintomas">
                        Sim
                    </label>
                    <label :class="{ 'checked': sintomas === 'nao' }">
                        <input type="radio" value="nao" v-model="sintomas">
                        Não
                    </label>
                </div>
                <div class="info">
                    <h4 class="info-h4">Você está com algum sintoma de alergia?</h4>
                    <span class="info-icon" @mouseover="showInfo = true" @mouseleave="showInfo = false">i</span>
                    <div v-if="showInfo" class="info-popup">
                        <p>Exemplos: urticária, rinite, dermatite...</p>
                    </div>
                </div>
                <div class="radio-group-small">
                    <label :class="{ 'checked': alergia === 'sim' }">
                        <input type="radio" value="sim" v-model="alergia">
                        Sim
                    </label>
                    <label :class="{ 'checked': alergia === 'nao' }">
                        <input type="radio" value="nao" v-model="alergia">
                        Não
                    </label>
                </div>
            </div>

            <!-- Step 5: Vacinação -->
            <div v-if="step === 5">
                <h4>Você se vacinou nos últimos 30 dias?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': vacinacao === 'sim' }">
                        <input type="radio" value="sim" v-model="vacinacao">
                        Sim
                    </label>
                    <label :class="{ 'checked': vacinacao === 'nao' }">
                        <input type="radio" value="nao" v-model="vacinacao">
                        Não
                    </label>
                </div>
                <div v-if="vacinacao === 'sim'">
                    <h4>Selecione as vacinas que você tomou e a data:</h4>
                    <div v-for="(vacina, index) in vacinas" :key="index">
                        <select v-model="vacina.selected">
                            <option v-for="option in listaVacinas" :key="option.value" :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div class="add_item">
                            <input type="date" placeholder="data:" v-model="vacina.diaVacina"
                                onchange="this.className=(this.value!=''?'has-value':'')"
                                @change="daysFromVaccination(index)">
                            <button v-if="index === vacinas.length - 1" @click="addVaccine" class="button_add">Adicionar
                                outra vacina</button>
                        </div>
                        <div v-if="vacina.selected === 'covid'">
                            <h4>A vacina que você tomou do Covid-19 foi a Coronavac ou a Covaxin?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': vacina.coronavac === 'sim' }">
                                    <input type="radio" value="sim" v-model="vacina.coronavac">
                                    Sim
                                </label>
                                <label :class="{ 'checked': vacina.coronavac === 'nao' }">
                                    <input type="radio" value="nao" v-model="vacina.coronavac">
                                    Não
                                </label>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Step 6: Cirurgias e procedimentos odontológicos -->
            <div v-if="step === 6">
                <h4>Você realizou algum procedimento ou cirurgia odontológica nos últimos 2 meses?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': odontologico === 'sim' }">
                        <input type="radio" value="sim" v-model="odontologico">
                        Sim
                    </label>
                    <label :class="{ 'checked': odontologico === 'nao' }">
                        <input type="radio" value="nao" v-model="odontologico">
                        Não
                    </label>
                </div>
                <div v-if="odontologico === 'sim'">
                    <h4>Selecione os procedimentos que você realizou e a data:</h4>
                    <div v-for="(procedimento, index) in procedimentos" :key="index">
                        <select v-model="procedimento.selected">
                            <option v-for="option in listaProcedimentosOdontologicos" :key="option.value"
                                :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div class="add_item">
                            <input type="date" placeholder="data:" v-model="procedimento.diaOdontologico"
                                onchange="this.className=(this.value!=''?'has-value':'')"
                                @change="daysFromDentalProcedure(index)">
                            <button v-if="index === procedimentos.length - 1" @click="addProcedure"
                                class="button_add">Adicionar outro procedimento</button>
                        </div>
                        <div v-if="['ajuste_aparelho', 'carie_sem_anestesia'].includes(procedimento.selected)">
                            <h4>Houve sangramento no procedimento?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': procedimento.sangramento === 'sim' }">
                                    <input type="radio" value="sim" v-model="procedimento.sangramento">
                                    Sim
                                </label>
                                <label :class="{ 'checked': procedimento.sangramento === 'nao' }">
                                    <input type="radio" value="nao" v-model="procedimento.sangramento">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['drenagem_abscesso', 'tratamento_canal', 'tratamento_gengivites', 'outras_cirurgias'].includes(procedimento.selected)">
                            <h4>Você tomou medicação?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': procedimento.medicacaoDental === 'sim' }">
                                    <input type="radio" value="sim" v-model="procedimento.medicacaoDental">
                                    Sim
                                </label>
                                <label :class="{ 'checked': procedimento.medicacaoDental === 'nao' }">
                                    <input type="radio" value="nao" v-model="procedimento.medicacaoDental">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="procedimento.selected === 'implante_dentario'">
                            <h4>Você está assintomático?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': procedimento.assintomaticosDental === 'sim' }">
                                    <input type="radio" value="sim" v-model="procedimento.assintomaticosDental">
                                    Sim
                                </label>
                                <label :class="{ 'checked': procedimento.assintomaticosDental === 'nao' }">
                                    <input type="radio" value="nao" v-model="procedimento.assintomaticosDental">
                                    Não
                                </label>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Step 7: Cirurgias -->
            <div v-if="step === 7">
                <h4>Você já realizou alguma cirurgia?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': cirurgiaRealizada === 'sim' }">
                        <input type="radio" value="sim" v-model="cirurgiaRealizada">
                        Sim
                    </label>
                    <label :class="{ 'checked': cirurgiaRealizada === 'nao' }">
                        <input type="radio" value="nao" v-model="cirurgiaRealizada">
                        Não
                    </label>
                </div>
                <div v-if="cirurgiaRealizada === 'sim'">
                    <h4>Selecione as cirurgias que você realizou:</h4>
                    <div v-for="(cirurgia, index) in cirurgias" :key="index">
                        <select v-model="cirurgia.selected">
                            <option v-for="option in listaCirurgias" :key="option.value" :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div v-if="cirurgia.selected === 'cateterismo_cardiaco'">
                            <h4>O resultado foi normal?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaNormal === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgiaNormal">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaNormal === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgiaNormal">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['cirurgias_adenoma_prostatico', 'cirurgias_de_paratireoide', 'cirurgias_de_tireoide', 'exames_com_contrastes_baritado', 'laparoscopia', 'laparotomia_branca', 'nefrectomia_pos-trauma'].includes(cirurgia.selected)">
                            <h4>Você possui o laudo médico?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaLaudo === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgiaLaudo">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaLaudo === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgiaLaudo">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'cirurgia_de_malformacao_renal'">
                            <h4>Você ficou com alguma sequela funcional após essa cirurgia de malformação renal?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaSequelaFuncional === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgiaSequelaFuncional">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaSequelaFuncional === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgiaSequelaFuncional">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'cirurgias_oftalmologicas_com_acesso_ao_snc'">
                            <h4>Você ficou com alguma sequela após essa cirurgia oftalmológica?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaSequela === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgiaSequela">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaSequela === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgiaSequela">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'curetagem'">
                            <h4>Qual foi o motivo de você ter feito essa curetagem?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaCuretagem === 'aborto' }">
                                    <input type="radio" value="aborto" v-model="cirurgia.cirurgiaCuretagem">
                                    Pós-aborto
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaCuretagem === 'outro' }">
                                    <input type="radio" value="outro" v-model="cirurgia.cirurgiaCuretagem">
                                    Outro motivo
                                </label>
                            </div>
                            <div v-if="cirurgia.cirurgiaCuretagem === 'outro'">
                                <h4>Você já está curado?</h4>
                                <div class="radio-group-small">
                                    <label :class="{ 'checked': cirurgia.cirurgiaCura === 'sim' }">
                                        <input type="radio" value="sim" v-model="cirurgia.cirurgiaCura">
                                        Sim
                                    </label>
                                    <label :class="{ 'checked': cirurgia.cirurgiaCura === 'nao' }">
                                        <input type="radio" value="nao" v-model="cirurgia.cirurgiaCura">
                                        Não
                                    </label>
                                </div>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'gastrectomia_vertical'">
                            <h4>Essa cirurgia foi feita nos últimos 2 anos?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia2anos === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia2anos">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia2anos === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia2anos">
                                    Não
                                </label>
                            </div>
                            <div v-if="cirurgia.cirurgia2anos === 'nao'">
                                <h4>Você toma alguma medicação devido a essa cirurgia?</h4>
                                <div class="radio-group-small">
                                    <label :class="{ 'checked': cirurgia.cirurgiaMedicamento === 'sim' }">
                                        <input type="radio" value="sim" v-model="cirurgia.cirurgiaMedicamento">
                                        Sim
                                    </label>
                                    <label :class="{ 'checked': cirurgia.cirurgiaMedicamento === 'nao' }">
                                        <input type="radio" value="nao" v-model="cirurgia.cirurgiaMedicamento">
                                        Não
                                    </label>
                                </div>
                                <h4>Você fez algum exame laboratorial nos últimos 3 meses que tenha resultados de
                                    hemograma, ferro, B12 e cálcio?</h4>
                                <div class="radio-group-small">
                                    <label :class="{ 'checked': cirurgia.cirurgiaExame === 'sim' }">
                                        <input type="radio" value="sim" v-model="cirurgia.cirurgiaExame">
                                        Sim
                                    </label>
                                    <label :class="{ 'checked': cirurgia.cirurgiaExame === 'nao' }">
                                        <input type="radio" value="nao" v-model="cirurgia.cirurgiaExame">
                                        Não
                                    </label>
                                </div>
                            </div>
                        </div>
                        <div
                            v-if="cirurgia.selected === 'exames_com_contrastes_baritado' && cirurgia.cirurgiaLaudo === 'sim'">
                            <h4>Você fez esse exame com contraste baritado nas últimas 24 horas?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia24horas === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia24horas">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia24horas === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia24horas">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'diu'">
                            <h4>Você colocou esse diu nas últimas 48 horas?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia48horas === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia48horas">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia48horas === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia48horas">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'esclerose_de_varizes_de_membros_inferiores'">
                            <h4>Você fez esse procedimento nos últimos 3 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia3dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia3dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia3dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia3dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'cintilografia'">
                            <h4>Essa cirurgia foi feita nos últimos 7 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia7dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia7dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia7dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia7dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'infiltracao_articular'">
                            <h4>Essa cirurgia foi feita nos últimos 15 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia15dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia15dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia15dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia15dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['exames_com_contrastes_aereos', 'exames_com_contrastes_iodado_ou_gadolinio', 'mielografia'].includes(cirurgia.selected) || (cirurgia.selected === 'cateterismo_cardiaco' && cirurgia.cirurgiaNormal === 'sim')">
                            <h4>Essa cirurgia foi feita nos últimos 30 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia30dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia30dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia30dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia30dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'litotripsia'">
                            <h4>Essa cirurgia foi feita nos últimos 60 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia60dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia60dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia60dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia60dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['amigdalectomia', 'apendicectomia', 'cirurgias_plasticas_pequeno_porte', 'cirurgia_varizes', 'extracao_calculos', 'hemorroidectomia', 'hernioplastia', 'pleurostomia'].includes(cirurgia.selected) || (cirurgia.selected === 'cirurgias_adenoma_prostatico' && cirurgia.cirurgiaLaudo === 'sim') || (cirurgia.selected === 'cirurgias_oftalmologicas_com_acesso_ao_snc' && cirurgia.cirurgiaSequela === 'nao') || (cirurgia.selected === 'curetagem' && cirurgia.cirurgiaCuretagem === 'aborto') || (cirurgia.selected === 'laparotomia_branca' && cirurgia.cirurgiaLaudo === 'sim')">
                            <h4>Essa cirurgia foi feita nos últimos 3 meses?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia3meses === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia3meses">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia3meses === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia3meses">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['artrodese_de_coluna', 'artroscopia', 'cirurgias_endoscopicas', 'cirurugias_ginecologicas_de_grande_porte', 'cirurgias_ortopedicas', 'cirurgias_patalogicas_benignas_da_mama', 'cirurgias_plasticas_medio_porte', 'colecistectomia', 'laminectomia', 'lipoaspiracao'].includes(cirurgia.selected) || (cirurgia.selected === 'cirurgias_de_paratireoide' && cirurgia.cirurgiaLaudo === 'sim') || (cirurgia.selected === 'cirurgias_de_tireoide' && cirurgia.cirurgiaLaudo === 'sim') || (cirurgia.selected === 'cirurgia_de_malformacao_renal' && cirurgia.cirurgiaSequelaFuncional === 'nao') || (cirurgia.selected === 'laparoscopia' && cirurgia.cirurgiaLaudo === 'sim')">
                            <h4>Essa cirurgia foi feita nos últimos 6 meses?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia6meses === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia6meses">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia6meses === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia6meses">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['colectomia', 'enxertos_heterologos_tecidos', 'esplenectomia_pos-traumatica', 'hepatectomia'].includes(cirurgia.selected) || (cirurgia.selected === 'nefrectomia_pos-trauma' && cirurgia.cirurgiaLaudo === 'sim')">
                            <h4>Essa cirurgia foi feita nos últimos 12 meses?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgia6meses === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgia6meses">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgia6meses === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgia6meses">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="['cirurgias_dermatologicas_de_pequeno_porte', 'cirurgias_oftalmologicas_de_pequeno_porte', 'cirurgias_urologicas_pequeno_porte'].includes(cirurgia.selected)">
                            <h4>Você teve alta dessa cirurgia há mais de 30 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaAlta30dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgiaAlta30dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaAlta30dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgiaAlta30dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="cirurgia.selected === 'cirurgias_ginecologicas_de_pequeno_porte'">
                            <h4>Você teve alta dessa cirurgia há mais de 3 meses?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': cirurgia.cirurgiaAlta3meses === 'sim' }">
                                    <input type="radio" value="sim" v-model="cirurgia.cirurgiaAlta3meses">
                                    Sim
                                </label>
                                <label :class="{ 'checked': cirurgia.cirurgiaAlta3meses === 'nao' }">
                                    <input type="radio" value="nao" v-model="cirurgia.cirurgiaAlta3meses">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div class="add_item">
                            <button v-if="index === cirurgias.length - 1" @click="addSurgery"
                                class="button_add">Adicionar outra cirurgia</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Step 8: Doenças -->
            <div v-if="step === 8">
                <h4>Você tem alguma doença?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': doencaExistente === 'sim' }">
                        <input type="radio" value="sim" v-model="doencaExistente">
                        Sim
                    </label>
                    <label :class="{ 'checked': doencaExistente === 'nao' }">
                        <input type="radio" value="nao" v-model="doencaExistente">
                        Não
                    </label>
                </div>
                <div v-if="doencaExistente === 'sim'">
                    <h4>Selecione as doenças que você tem:</h4>
                    <div v-for="(doenca, index) in doencas" :key="index">
                        <select v-model="doenca.selected">
                            <option v-for="option in listaDoencas" :key="option.value" :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div class="add_item">
                            <button v-if="index === doencas.length - 1" @click="addDiseases"
                                class="button_add">Adicionar outra doença</button>
                        </div>
                    </div>
                </div>
                <p>*As doenças ainda não foram implementados no site</p>
            </div>

            <!-- Step 9: Remédios -->
            <div v-if="step === 9">
                <h4>Você toma algum remédio ou tomou nos últimos 30 dias?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': remedioTomado === 'sim' }">
                        <input type="radio" value="sim" v-model="remedioTomado">
                        Sim
                    </label>
                    <label :class="{ 'checked': remedioTomado === 'nao' }">
                        <input type="radio" value="nao" v-model="remedioTomado">
                        Não
                    </label>
                </div>
                <div v-if="remedioTomado === 'sim'">
                    <h4>Selecione-os:</h4>
                    <div v-for="(remedio, index) in remedios" :key="index">
                        <select v-model="remedio.selected">
                            <option v-for="option in listaRemedios" :key="option.value" :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div v-if="remedio.selected === 'antibiotico'">
                            <h4>O antibiótico que você tomou foi Penicilina Benzatina?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': remedio.remedioAntibioticoPenicilina === 'sim' }">
                                    <input type="radio" value="sim" v-model="remedio.remedioAntibioticoPenicilina">
                                    Sim
                                </label>
                                <label :class="{ 'checked': remedio.remedioAntibioticoPenicilina === 'nao' }">
                                    <input type="radio" value="nao" v-model="remedio.remedioAntibioticoPenicilina">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div v-if="remedio.selected === 'anti-inflamatorio'">
                            <h4>Você tomou esse remédio nos últimos 7 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': remedio.remedio7dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="remedio.remedio7dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': remedio.remedio7dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="remedio.remedio7dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="remedio.selected === 'antibiotico' && remedio.remedioAntibioticoPenicilina === 'nao'">
                            <h4>Você tomou esse remédio nos últimos 15 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': remedio.remedio15dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="remedio.remedio15dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': remedio.remedio15dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="remedio.remedio15dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div
                            v-if="remedio.selected === 'antibiotico' && remedio.remedioAntibioticoPenicilina === 'sim'">
                            <h4>Você tomou esse remédio nos últimos 30 dias?</h4>
                            <div class="radio-group-small">
                                <label :class="{ 'checked': remedio.remedio30dias === 'sim' }">
                                    <input type="radio" value="sim" v-model="remedio.remedio30dias">
                                    Sim
                                </label>
                                <label :class="{ 'checked': remedio.remedio30dias === 'nao' }">
                                    <input type="radio" value="nao" v-model="remedio.remedio30dias">
                                    Não
                                </label>
                            </div>
                        </div>
                        <div class="add_item">
                            <button v-if="index === remedios.length - 1" @click="addMedications"
                                class="button_add">Adicionar outro remédio</button>
                        </div>
                    </div>
                </div>
                <p
                    v-if="procedimentos.some(procedimento => procedimento.medicacaoDental === 'sim') || procedimentos.some(procedimento => procedimento.selected === 'extracao_dentaria')">
                    *Não se esqueça de colocar os medicamentos que você tomou devido ao procedimento odontológico
                    realizado</p>
                <p>*Nem todos os medicamentos já foram implementados no site</p>
            </div>

            <!-- Step 10: Soro -->
            <div v-if="step === 10">
                <h4>Você tomou soro nos últimos 12 meses?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': soro === 'sim' }">
                        <input type="radio" value="sim" v-model="soro">
                        Sim
                    </label>
                    <label :class="{ 'checked': soro === 'nao' }">
                        <input type="radio" value="nao" v-model="soro">
                        Não
                    </label>
                </div>
                <div v-if="soro === 'sim'">
                    <h4>Foi de que tipo?</h4>
                    <div class="radio-group">
                        <label :class="{ 'checked': soroTipo === 'normal' }">
                            <input type="radio" value="normal" v-model="soroTipo">
                            Imunoterapia passiva heteróloga (soro)
                        </label>
                        <label :class="{ 'checked': soroTipo === 'humano' }">
                            <input type="radio" value="humano" v-model="soroTipo">
                            Imunoterapia passiva homóloga (soro humano)
                        </label>
                    </div>
                </div>
                <div v-if="soroTipo === 'normal'">
                    <h4>Foi nas últimas 4 semanas?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': soro4Semanas === 'sim' }">
                            <input type="radio" value="sim" v-model="soro4Semanas">
                            Sim
                        </label>
                        <label :class="{ 'checked': soro4Semanas === 'nao' }">
                            <input type="radio" value="nao" v-model="soro4Semanas">
                            Não
                        </label>
                    </div>
                </div>
            </div>

            <!-- Step 11: Procedimentos terapêuticos e estéticos invasivos -->
            <div v-if="step === 11">
                <h3>Procedimentos terapêuticos e estéticos invasivos</h3>
                <h4>Você fez alguma tatuagem nos últimos 12 meses?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': tatuagem === 'sim' }">
                        <input type="radio" value="sim" v-model="tatuagem">
                        Sim
                    </label>
                    <label :class="{ 'checked': tatuagem === 'nao' }">
                        <input type="radio" value="nao" v-model="tatuagem">
                        Não
                    </label>
                </div>
                <div v-if="tatuagem === 'sim'">
                    <h4>Você possui a foto do registro do estúdio na Vigilância Sanitária e a nota fiscal/recibo do
                        estabelecimento com a comprovação da data do procedimento no local?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': tatuagemRegistro === 'sim' }">
                            <input type="radio" value="sim" v-model="tatuagemRegistro">
                            Sim
                        </label>
                        <label :class="{ 'checked': tatuagemRegistro === 'nao' }">
                            <input type="radio" value="nao" v-model="tatuagemRegistro">
                            Não
                        </label>
                    </div>
                </div>
                <div v-if="tatuagemRegistro === 'sim'">
                    <h4>O procedimento foi feito nos últimos 6 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': tatuagem6 === 'sim' }">
                            <input type="radio" value="sim" v-model="tatuagem6">
                            Sim
                        </label>
                        <label :class="{ 'checked': tatuagem6 === 'nao' }">
                            <input type="radio" value="nao" v-model="tatuagem6">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você fez botox, eletrolise, carboxiterapia, mesoterapia, micropigmentação, preenchimento com
                    colágeno e ácido hialurônico ou pelling nos últimos 6 meses?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': botox === 'sim' }">
                        <input type="radio" value="sim" v-model="botox">
                        Sim
                    </label>
                    <label :class="{ 'checked': botox === 'nao' }">
                        <input type="radio" value="nao" v-model="botox">
                        Não
                    </label>
                </div>
                <div v-if="botox === 'sim'">
                    <h4>Foi feito em uma clínica especializada?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': botoxEspecializado === 'sim' }">
                            <input type="radio" value="sim" v-model="botoxEspecializado">
                            Sim
                        </label>
                        <label :class="{ 'checked': botoxEspecializado === 'nao' }">
                            <input type="radio" value="nao" v-model="botoxEspecializado">
                            Não
                        </label>
                    </div>
                </div>
                <div v-if="botoxEspecializado === 'sim'">
                    <h4>O procedimento foi feito nos últimos 3 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': botox3 === 'sim' }">
                            <input type="radio" value="sim" v-model="botox3">
                            Sim
                        </label>
                        <label :class="{ 'checked': botox3 === 'nao' }">
                            <input type="radio" value="nao" v-model="botox3">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você fez acupuntura nos últimos 12 meses?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': acupuntura === 'sim' }">
                        <input type="radio" value="sim" v-model="acupuntura">
                        Sim
                    </label>
                    <label :class="{ 'checked': acupuntura === 'nao' }">
                        <input type="radio" value="nao" v-model="acupuntura">
                        Não
                    </label>
                </div>
                <div v-if="acupuntura === 'sim'">
                    <h4>Foi realizado em clínicas e com profissionais com autorização da Vigilância Sanitária?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': acupunturaAutorizada === 'sim' }">
                            <input type="radio" value="sim" v-model="acupunturaAutorizada">
                            Sim
                        </label>
                        <label :class="{ 'checked': acupunturaAutorizada === 'nao' }">
                            <input type="radio" value="nao" v-model="acupunturaAutorizada">
                            Não
                        </label>
                    </div>
                </div>
                <div v-if="acupunturaAutorizada === 'sim'">
                    <h4>A acupuntura foi realizada nas últimas 72 horas?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': acupuntura72 === 'sim' }">
                            <input type="radio" value="sim" v-model="acupuntura72">
                            Sim
                        </label>
                        <label :class="{ 'checked': acupuntura72 === 'nao' }">
                            <input type="radio" value="nao" v-model="acupuntura72">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você colocou um piercing nos últimos 12 meses?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': piercing === 'sim' }">
                        <input type="radio" value="sim" v-model="piercing">
                        Sim
                    </label>
                    <label :class="{ 'checked': piercing === 'nao' }">
                        <input type="radio" value="nao" v-model="piercing">
                        Não
                    </label>
                </div>
                <div v-if="piercing === 'sim'">
                    <h4>Você possui a foto do registro do estúdio na Vigilância Sanitária e a nota fiscal/recibo do
                        estabelecimento com a comprovação da data do procedimento no local?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': piercingRegistro === 'sim' }">
                            <input type="radio" value="sim" v-model="piercingRegistro">
                            Sim
                        </label>
                        <label :class="{ 'checked': piercingRegistro === 'nao' }">
                            <input type="radio" value="nao" v-model="piercingRegistro">
                            Não
                        </label>
                    </div>
                </div>
                <div v-if="piercingRegistro === 'sim'">
                    <h4>Esse piercing foi colocado nos últimos 6 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': piercing6 === 'sim' }">
                            <input type="radio" value="sim" v-model="piercing6">
                            Sim
                        </label>
                        <label :class="{ 'checked': piercing6 === 'nao' }">
                            <input type="radio" value="nao" v-model="piercing6">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você tem ou já teve um piercing em cavidade oral ou órgão genital?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': piercingCavidade === 'agora' }">
                        <input type="radio" value="agora" v-model="piercingCavidade">
                        Tenho
                    </label>
                    <label :class="{ 'checked': piercingCavidade === 'recente' }">
                        <input type="radio" value="recente" v-model="piercingCavidade">
                        Já tive
                    </label>
                    <label :class="{ 'checked': piercingCavidade === 'nunca' }">
                        <input type="radio" value="nunca" v-model="piercingCavidade">
                        Nunca tive
                    </label>
                </div>
                <div v-if="piercingCavidade === 'recente'">
                    <h4>Esse piercing foi retirado nos últimos 12 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': piercingRetirado === 'sim' }">
                            <input type="radio" value="sim" v-model="piercingRetirado">
                            Sim
                        </label>
                        <label :class="{ 'checked': piercingRetirado === 'nao' }">
                            <input type="radio" value="nao" v-model="piercingRetirado">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você fez um exame ou procedimento endoscópico nos últimos 6 meses?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': endoscopia === 'sim' }">
                        <input type="radio" value="sim" v-model="endoscopia">
                        Sim
                    </label>
                    <label :class="{ 'checked': endoscopia === 'nao' }">
                        <input type="radio" value="nao" v-model="endoscopia">
                        Não
                    </label>
                </div>
            </div>

            <!-- Step 12: Drogas lícitas ou ilícitas -->
            <div v-if="step === 12">
                <h3>Drogas lícitas e ilícitas</h3>
                <h4>Você consumiu bebidas alcoolicas nas últimas 12 horas?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaBebida === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaBebida">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaBebida === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaBebida">
                        Não
                    </label>
                </div>
                <div v-if="drogaBebida === 'sim'">
                    <h4>Você sofre de alcoolismo crônico?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': drogaAlcolatra === 'sim' }">
                            <input type="radio" value="sim" v-model="drogaAlcolatra">
                            Sim
                        </label>
                        <label :class="{ 'checked': drogaAlcolatra === 'nao' }">
                            <input type="radio" value="nao" v-model="drogaAlcolatra">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você fuma cigarro (incluindo cigarros eletrônicos)?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaCigarro === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaCigarro">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaCigarro === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaCigarro">
                        Não
                    </label>
                </div>
                <h4>Você utilizou anabolizantes injetáveis nos últimos 12 meses?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaAnabolizante === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaAnabolizante">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaAnabolizante === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaAnabolizante">
                        Não
                    </label>
                </div>
                <h4>Você fuma maconha ou narguillé?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaMaconha === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaMaconha">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaMaconha === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaMaconha">
                        Não
                    </label>
                </div>
                <h4>Você utilizou Ayahuasca (Santo-Daime) nos últimos 30 dias?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaAyahuasca === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaAyahuasca">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaAyahuasca === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaAyahuasca">
                        Não
                    </label>
                </div>
                <div class="info">
                    <h4 class="info-h4">Você fez uso de drogas inaláveis, sintéticas ou alucionógenos nos últimos 12
                        meses?</h4>
                    <span class="info-icon" @mouseover="showInfo = true" @mouseleave="showInfo = false">i</span>
                    <div v-if="showInfo" class="info-popup">
                        <p>Exemplos:</p>
                        <p>Drogas inaláveis: cocaína, crack...</p>
                        <p>Drogas sintéticas: ecstasy/MDMA, metanfetamina...</p>
                        <p>Alucionógenos: cogumelos, LSD...</p>
                    </div>
                </div>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaGerais === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaGerais">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaGerais === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaGerais">
                        Não
                    </label>
                </div>
                <h4>Você já usou drogas ilegais injetáveis?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': drogaInjetavel === 'sim' }">
                        <input type="radio" value="sim" v-model="drogaInjetavel">
                        Sim
                    </label>
                    <label :class="{ 'checked': drogaInjetavel === 'nao' }">
                        <input type="radio" value="nao" v-model="drogaInjetavel">
                        Não
                    </label>
                </div>
            </div>

            <!-- Step 13: Vida sexual -->
            <div v-if="step === 13">
                <h4>Você tem uma vida sexual ativa?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': sexo === 'sim' }">
                        <input type="radio" value="sim" v-model="sexo">
                        Sim
                    </label>
                    <label :class="{ 'checked': sexo === 'nao' }">
                        <input type="radio" value="nao" v-model="sexo">
                        Não
                    </label>
                </div>
                <div v-if="sexo === 'sim'">
                    <h4>Você teve mais de 3 parcerias sexuais nos últimos 6 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': sexo3Parcerias === 'sim' }">
                            <input type="radio" value="sim" v-model="sexo3Parcerias">
                            Sim
                        </label>
                        <label :class="{ 'checked': sexo3Parcerias === 'nao' }">
                            <input type="radio" value="nao" v-model="sexo3Parcerias">
                            Não
                        </label>
                    </div>
                    <h4>Você fez sexo casual, com desconhecidos ou profissional do sexo nos últimos 12 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': sexoCasual === 'sim' }">
                            <input type="radio" value="sim" v-model="sexoCasual">
                            Sim
                        </label>
                        <label :class="{ 'checked': sexoCasual === 'nao' }">
                            <input type="radio" value="nao" v-model="sexoCasual">
                            Não
                        </label>
                    </div>
                    <h4>Você fez sexo com mais de uma parceria sexual simultaneamente nos últimos 12 meses?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': sexoSimultaneo === 'sim' }">
                            <input type="radio" value="sim" v-model="sexoSimultaneo">
                            Sim
                        </label>
                        <label :class="{ 'checked': sexoSimultaneo === 'nao' }">
                            <input type="radio" value="nao" v-model="sexoSimultaneo">
                            Não
                        </label>
                    </div>
                </div>
            </div>

            <!-- Step 14: Risco acrescido para transmissão de doenças devido à viagens -->
            <div v-if="step === 14">
                <h3>Risco acrescido para transmissão de doenças devido à viagens</h3>
                <div v-if="idade > 27">
                    <h4>Você morou no Reino Unido por mais de 3 meses entre 1980 e 1996?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': viagemReinoUnido === 'sim' }">
                            <input type="radio" value="sim" v-model="viagemReinoUnido">
                            Sim
                        </label>
                        <label :class="{ 'checked': viagemReinoUnido === 'nao' }">
                            <input type="radio" value="nao" v-model="viagemReinoUnido">
                            Não
                        </label>
                    </div>
                </div>
                <h4>Você morou na Europa por mais de 5 anos após 1980?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': viagemEuropa === 'sim' }">
                        <input type="radio" value="sim" v-model="viagemEuropa">
                        Sim
                    </label>
                    <label :class="{ 'checked': viagemEuropa === 'nao' }">
                        <input type="radio" value="nao" v-model="viagemEuropa">
                        Não
                    </label>
                </div>
                <h4>Você visitou área endêmica para Malária nos últimos 30 dias?</h4>
                <div class="radio-group-small">
                    <label :class="{ 'checked': viagemMalaria === 'sim' }">
                        <input type="radio" value="sim" v-model="viagemMalaria">
                        Sim
                    </label>
                    <label :class="{ 'checked': viagemMalaria === 'nao' }">
                        <input type="radio" value="nao" v-model="viagemMalaria">
                        Não
                    </label>
                </div>
            </div>

            <!-- Step 15: condição ginecológica da mulher -->
            <div v-if="step === 15">
                <div v-if="genero === 'feminino'">
                    <h3>Condição ginecológica da mulher</h3>
                    <h4>Você está grávida ou esteve grávida nos últimos 12 meses?</h4>
                    <div class="radio-group">
                        <label :class="{ 'checked': gravidez === 'agora' }">
                            <input type="radio" value="agora" v-model="gravidez">
                            Estou grávida
                        </label>
                        <label :class="{ 'checked': gravidez === 'recente' }">
                            <input type="radio" value="recente" v-model="gravidez">
                            Estive nos últimos 12 meses
                        </label>
                        <label :class="{ 'checked': gravidez === 'nao' }">
                            <input type="radio" value="nao" v-model="gravidez">
                            Não
                        </label>
                    </div>
                    <div v-if="gravidez === 'recente'">
                        <h4>Você teve um parto normal, cesariana ou aborto?</h4>
                        <div class="radio-group">
                            <label :class="{ 'checked': parto === 'normal' }">
                                <input type="radio" value="normal" v-model="parto">
                                Parto normal
                            </label>
                            <label :class="{ 'checked': parto === 'cesariana' }">
                                <input type="radio" value="cesariana" v-model="parto">
                                Cesariana
                            </label>
                            <label :class="{ 'checked': parto === 'aborto' }">
                                <input type="radio" value="aborto" v-model="parto">
                                Aborto
                            </label>
                        </div>
                    </div>
                    <div v-if="parto === 'normal'">
                        <h4>Seu parto foi nos últimos 3 meses?</h4>
                        <div class="radio-group-small">
                            <label :class="{ 'checked': partoRecente === 'sim' }">
                                <input type="radio" value="sim" v-model="partoRecente">
                                Sim
                            </label>
                            <label :class="{ 'checked': partoRecente === 'nao' }">
                                <input type="radio" value="nao" v-model="partoRecente">
                                Não
                            </label>
                        </div>
                    </div>
                    <div v-if="parto === 'cesariana'">
                        <h4>Sua cesariana foi nos últimos 6 meses?</h4>
                        <div class="radio-group-small">
                            <label :class="{ 'checked': cesarianaRecente === 'sim' }">
                                <input type="radio" value="sim" v-model="cesarianaRecente">
                                Sim
                            </label>
                            <label :class="{ 'checked': cesarianaRecente === 'nao' }">
                                <input type="radio" value="nao" v-model="cesarianaRecente">
                                Não
                            </label>
                        </div>
                    </div>
                    <div v-if="parto === 'normal' || parto === 'cesariana'">
                        <h4>Você está amamentando?</h4>
                        <div class="radio-group-small">
                            <label :class="{ 'checked': amamentacao === 'sim' }">
                                <input type="radio" value="sim" v-model="amamentacao">
                                Sim
                            </label>
                            <label :class="{ 'checked': amamentacao === 'nao' }">
                                <input type="radio" value="nao" v-model="amamentacao">
                                Não
                            </label>
                        </div>
                    </div>
                    <div v-if="parto === 'aborto'">
                        <h4>Seu aborto foi nos últimos 3 meses?</h4>
                        <div class="radio-group-small">
                            <label :class="{ 'checked': abortoRecente === 'sim' }">
                                <input type="radio" value="sim" v-model="abortoRecente">
                                Sim
                            </label>
                            <label :class="{ 'checked': abortoRecente === 'nao' }">
                                <input type="radio" value="nao" v-model="abortoRecente">
                                Não
                            </label>
                        </div>
                    </div>
                    <h4>Você já teve 3 ou mais abortos?</h4>
                    <div class="radio-group-small">
                        <label :class="{ 'checked': abortoRepeticao === 'sim' }">
                            <input type="radio" value="sim" v-model="abortoRepeticao">
                            Sim
                        </label>
                        <label :class="{ 'checked': abortoRepeticao === 'nao' }">
                            <input type="radio" value="nao" v-model="abortoRepeticao">
                            Não
                        </label>
                    </div>
                </div>
                <div v-else>
                    <h2>Clique em finalizar para ver o resultado do teste. Se quiser você pode voltar para conferir as
                        respostas</h2>
                </div>
            </div>

            <!-- Step 16: Resultado -->
            <div v-if="step === 16">
                <div v-if="motivosInaptidaoDefinitiva.length > 0">
                    <h2>Você é <strong>inapto definitivo</strong> para doar sangue</h2>
                    <h4>Motivo(s):</h4>
                    <p v-for="(motivoD, index) in motivosInaptidaoDefinitiva" :key="index">{{ motivoD }}</p>
                </div>
                <div v-else-if="motivosInaptidao.length > 0">
                    <h2>Você está <strong>inapto</strong> para doar</h2>
                    <h4>Motivo(s):</h4>
                    <p v-for="(motivo, index) in motivosInaptidao" :key="index">{{ motivo }}</p>
                    <p>- Lembre-se de fazer o teste novamente quando passar o tempo de inaptidão temporária para ver se
                        você está apto para doar. Marque no seu calendário!</p>
                </div>
                <div v-else>
                    <h2>Você está <strong>apto</strong> para doar</h2>
                    <p v-if="this.idade >= 16 && this.idade < 18 && this.autorizacao === 'sim'">- Como você é menor de
                        idade, lembre-se de que para doar você precisa ter a permissão dos seus pais ou responsável
                        legal e estar na companhia de um deles;</p>
                    <p
                        v-if="this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_de_tireoide' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia6meses === 'nao')">
                        - Cirurgias de tireoide são avaliadas caso a caso pelo triagista, mesmo tendo sido a mais de 6
                        meses e possuindo o laudo médico;</p>
                    <p
                        v-if="this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_dermatologicas_de_pequeno_porte' && cirurgia.cirurgiaAlta30dias === 'sim')">
                        - Cirurgias dermatológicas de pequeno porte são avaliadas caso a caso com histopatológico, mesmo
                        tendo sido a mais de 30 dias;</p>
                    <p v-if="this.drogaMaconha === 'sim'">- Você precisa passar 12 horas sem fumar maconha ou narguillé
                        para poder doar;</p>
                    <p v-if="this.drogaCigarro === 'sim'">- Lembre-se de que você não deve fumar por duas horas após a
                        doação (incluindo cigarros eletrônicos);</p>
                    <p>- Lembre-se de estar bem alimentado e hidratado e de não ingerir bebida alcoólicas nas 12h que
                        antecedem a doação;</p>
                    <p>- Para mais informações sobre doenças e medicamentos, contate o Hemope, pois os critérios de
                        inaptidão para esses tópicos ainda não foram implementados no website.</p>
                </div>
            </div>
        </div>

        <!-- Navegação entre os steps -->
        <div class="navigation-buttons">
            <button class="button_prev" type="button" @click="prevStep"
                v-if="step > 1 && step < numberSteps">Anterior</button>
            <button class="button_next" type="button" @click="nextStep" v-if="step < (numberSteps - 1)">Próximo</button>
            <button class="button_finish" type="button" @click="nextStep"
                v-if="step === (numberSteps - 1)">Finalizar</button>
        </div>
    </form>
</template>

<script>
export default {
    name: 'Formulario',
    data() {
        return {
            step: 1,
            numberSteps: 16,
            showInfo: false,
            genero: '',
            idade: '',
            autorizacao: '',
            peso: '',
            altura: '',
            exame: '',
            doador: '',
            doacaoMulherRecente: '',
            doacaoHomemRecente: '',
            sintomas: '',
            alergia: '',
            vacinacao: '',
            diaVacina: '',
            diasDesdeVacinacao: '',
            listaVacinas: [
                { value: 'antirrabica_profilatica', text: 'Antirrábica profilática' },
                { value: 'antirrabica_apos_animal', text: 'Antirrábica após exposição animal' },
                { value: 'bcg', text: 'BCG' },
                { value: 'brucelose', text: 'Brucelose' },
                { value: 'caxumba', text: 'Caxumba (Parotidite)' },
                { value: 'colera', text: 'Cólera' },
                { value: 'coqueluche', text: 'Coqueluche' },
                { value: 'covid', text: 'Covid-19' },
                { value: 'dengue', text: 'Dengue' },
                { value: 'difteria', text: 'Difteria' },
                { value: 'febre_amarela', text: 'Febre amarela' },
                { value: 'febre_tifoide_oral', text: 'Febre Tifoide Oral' },
                { value: 'febre_tifoife_injetavel', text: 'Febre Tifoide (Injetável)' },
                { value: 'hepatite_a', text: 'Hepatite A' },
                { value: 'hepatite_b_recombinante', text: 'Hepatite B recombinante' },
                { value: 'hepatite_b_plasma', text: 'Hepatite B (derivada de plasma)' },
                { value: 'hpv', text: 'HPV' },
                { value: 'hemophillus', text: 'Hemophillus influenzae' },
                { value: 'influenza', text: 'Influenza (gripe)' },
                { value: 'leptospirose', text: 'Leptospirose' },
                { value: 'meningite', text: 'Meningite' },
                { value: 'peste', text: 'Peste' },
                { value: 'pneumococo', text: 'Pneumococo' },
                { value: 'polio_oral', text: 'Pólio Oral (Sabin)' },
                { value: 'polio', text: 'Pólio (Salk)' },
                { value: 'rubeola', text: 'Rubéola' },
                { value: 'sarampo', text: 'Sarampo' },
                { value: 'soro_antitetanico', text: 'Soro Antitetânico' },
                { value: 'tetano', text: 'Tétano' },
                { value: 'varicela', text: 'Varicela (Catapora)' },
                { value: 'variola', text: 'Varíola' },
            ],
            vacinas: [{
                selected: '',
                diaVacina: '',
                coronavac: ''
            }],
            odontologico: '',
            diaOdontologico: '',
            diasDesdeProcedimento: '',
            listaProcedimentosOdontologicos: [
                { value: 'ajuste_aparelho', text: 'Ajuste de aparelho ortodôntico' },
                { value: 'carie_sem_anestesia', text: 'Cárie sem anestesia' },
                { value: 'carie_com_anestesia', text: 'Cárie com anestesia local' },
                { value: 'drenagem_abscesso', text: 'Drenagem de abscesso' },
                { value: 'extracao_dentaria', text: 'Extração dentária' },
                { value: 'implante_dentario', text: 'Implante dentário' },
                { value: 'remocao_tartaro', text: 'Remoção de tártaro' },
                { value: 'tratamento_canal', text: 'Tratamento de canal' },
                { value: 'tratamento_dentario', text: 'Tratamento dentário com anestesia geral' },
                { value: 'tratamento_gengivites', text: 'Tratamento de gengivites' },
                { value: 'outros_procedimentos', text: 'Outros procedimentos com anestesia local' },
                { value: 'outras_cirurgias', text: 'Outras cirurgias com anestesia local' },
            ],
            procedimentos: [{
                selected: '',
                diaOdontologico: '',
                sangramento: '',
                medicacaoDental: '',
            }],
            cirurgiaRealizada: '',
            listaCirurgias: [
                { value: 'amigdalectomia', text: 'Amigdalectomia' },
                { value: 'apendicectomia', text: 'Apendicectomia' },
                { value: 'artrodese_de_coluna', text: 'Artrodese de coluna' },
                { value: 'artroscopia', text: 'Artroscopia' },
                { value: 'cateterismo_cardiaco', text: 'Cateterismo cardíaco' },
                { value: 'cintilografia', text: 'Cintilografia' },
                { value: 'cirurgias_vasculares_complexas', text: 'Cirurgias vasculares complexas' },
                { value: 'cirurgias_adenoma_prostatico', text: 'Cirurgias adenoma prostático' },
                { value: 'cirurgias_cardiacas', text: 'Cirurgias cardíacas' },
                { value: 'cirurgias_de_hipofise', text: 'Cirurgias de hipófise' },
                { value: 'cirurgias_de_paratireoide', text: 'Cirurgias de paratireoide' },
                { value: 'cirurgias_de_suprarrenal', text: 'Cirurgias de suprarrenal' },
                { value: 'cirurgias_de_tireoide', text: 'Cirurgias de tireoide' },
                { value: 'cirurgias_dermatologicas_de_pequeno_porte', text: 'Cirurgias dermatológicas de pequeno porte' },
                { value: 'cirurgias_endoscopicas', text: 'Cirurgias endoscópicas' },
                { value: 'cirurugias_ginecologicas_de_grande_porte', text: 'Cirurgias ginecológicas de grande porte' },
                { value: 'cirurgias_ginecologicas_de_pequeno_porte', text: 'Cirurgias ginecológicas de pequeno porte' },
                { value: 'cirurgia_de_malformacao_renal', text: 'Cirurgias de malformação renal' },
                { value: 'cirurgias_oftalmologicas_com_acesso_ao_snc', text: 'Cirurgias oftalmológicas com acesso ao SNC' },
                { value: 'cirurgias_oftalmologicas_de_pequeno_porte', text: 'Cirurgias oftalmológicas de pequeno porte (pterígio, catarata, miopia, laser)' },
                { value: 'cirurgias_ortopedicas', text: 'Cirurgias ortopédicas' },
                { value: 'cirurgias_patalogicas_benignas_da_mama', text: 'Cirurgias patológicas benignas da mama' },
                { value: 'cirurgias_plasticas_medio_porte', text: 'Cirurgias plásticas de médio e grande porte' },
                { value: 'cirurgias_plasticas_pequeno_porte', text: 'Cirurgias plásticas de pequeno porte' },
                { value: 'cirurgias_urologicas_pequeno_porte', text: 'Cirurgias urológicas de pequeno porte (vasectomia, fimose, hipo e epispádia)' },
                { value: 'cirurgia_varizes', text: 'Cirurgia de varizes em MMII' },
                { value: 'colecistectomia', text: 'Colecistectomia' },
                { value: 'colectomia', text: 'Colectomia' },
                { value: 'curetagem', text: 'Curetagem' },
                { value: 'diu', text: 'Diu (dispositivo intrauterino)' },
                { value: 'enterectomia', text: 'Enterectomia (retirada de segmento do intestino doente)' },
                { value: 'enxertos_heterologos_tecidos', text: 'Enxertos heterólogos de tecidos' },
                { value: 'esclerose_de_varizes_de_membros_inferiores', text: 'Esclerose de varizes de membros inferiores' },
                { value: 'esplenectomia', text: 'Esplenectomia' },
                { value: 'esplenectomia_pos-traumatica', text: 'Esplenectomia pós-traumática' },
                { value: 'exames_com_contrastes_aereos', text: 'Exames com contrastes aéreos' },
                { value: 'exames_com_contrastes_baritado', text: 'Exames com contrastes baritado' },
                { value: 'exames_com_contrastes_iodado_ou_gadolinio', text: 'Exames com contrastes iodado ou Gadolinio' },
                { value: 'extracao_calculos', text: 'Extração cálculos' },
                { value: 'gastrectomia_total_e_subtotal', text: 'Gastrectomia total e subtotal (incluindo cirurgia bariátrica e/ou colocação do anel) e gastroplastia' },
                { value: 'gastrectomia_vertical', text: 'Gastrectomia Vertical ou Sleeve Gástrico' },
                { value: 'hemorroidectomia', text: 'Hemorroidectomia' },
                { value: 'hepatectomia', text: 'Hepatectomia pós-trauma, doação, malformação' },
                { value: 'hernioplastia', text: 'Hernioplastia' },
                { value: 'infiltracao_articular', text: 'Infiltração articular' },
                { value: 'laminectomia', text: 'Laminectomia' },
                { value: 'laparoscopia', text: 'Laparoscopia' },
                { value: 'laparotomia_branca', text: 'Laparotomia branca' },
                { value: 'lipoaspiracao', text: 'Lipoaspiração' },
                { value: 'litotripsia', text: 'Litotripsia (a laser)' },
                { value: 'lobectomia', text: 'Lobectomia' },
                { value: 'mielografia', text: 'Mielografia' },
                { value: 'nefrectomia_por_patologias', text: 'Nefrectomia por patologias que não malformação renal' },
                { value: 'nefrectomia_pos-trauma', text: 'Nefrectomia pós-trauma, doação, malformação, pós infecção' },
                { value: 'pleurostomia', text: 'Pleurostomia' },
                { value: 'pneumectomia', text: 'Pneumectomia' },
            ],
            cirurgias: [{
                selected: '',
                cirurgiaNormal: '',
                cirurgiaLaudo: '',
                cirurgiaSequelaFuncional: '',
                cirurgiaSequela: '',
                cirurgiaCuretagem: '',
                cirurgiaCura: '',
                cirurgiaMedicamento: '',
                cirurgiaExame: '',
                cirurgia24horas: '',
                cirurgia48horas: '',
                cirurgia3dias: '',
                cirurgia7dias: '',
                cirurgia15dias: '',
                cirurgia30dias: '',
                cirurgia60dias: '',
                cirurgia3meses: '',
                cirurgia6meses: '',
                cirurgia12meses: '',
                cirurgia2anos: '',
                cirurgiaAlta30dias: '',
                cirurgiaAlta3meses: '',
            }],
            doencaExistente: '',
            listaDoencas: [],
            doencas: [{
                selected: '',
            }],
            remedioTomado: '',
            listaRemedios: [
                { value: 'anti-inflamatorio', text: 'Anti-inflamatórios' },
                { value: 'antibiotico', text: 'Antibióticos' },
            ],
            remedios: [{
                selected: '',
                remedioAntibioticoPenicilina: '',
                remedio7dias: '',
                remedio15dias: '',
                remedio30dias: '',
            }],
            tatuagem: '',
            tatuagemRegistro: '',
            tatuagem6: '',
            botox: '',
            botoxEspecializado: '',
            botox3: '',
            acupuntura: '',
            acupunturaAutorizada: '',
            acupuntura72: '',
            piercing: '',
            piercingRegistro: '',
            piercing6: '',
            piercingCavidade: '',
            piercingRetirado: '',
            endoscopia: '',
            drogaBebida: '',
            drogaAlcolatra: '',
            drogaCigarro: '',
            drogaAnabolizante: '',
            drogaMaconha: '',
            drogaAyahuasca: '',
            drogaGerais: '',
            drogaInjetavel: '',
            sexo: '',
            sexo3Parcerias: '',
            sexoCasual: '',
            sexoSimultaneo: '',
            viagemReinoUnido: '',
            viagemEuropa: '',
            viagemMalaria: '',
            soro: '',
            soroTipo: '',
            soro4Semanas: '',
            gravidez: '',
            parto: '',
            partoRecente: '',
            cesarianaRecente: '',
            amamentacao: '',
            abortoRecente: '',
            abortoRepeticao: '',
        };
    },
    computed: {
        // Barra de progresso
        progress() {
            if (this.step < this.numberSteps) {
                return ((this.step - 1) / (this.numberSteps - 2)) * 100;
            }
        },
        // Insere a vírgula automaticamente após o primeiro dígito da altura
        alturaFormatada() {
            if (!this.altura) return "";
            return this.altura.replace(/^(\d)(\d*)$/, "$1,$2");
        },
        // Cálculo do IMC
        imc() {
            if (this.peso && this.altura) {
                return (this.peso / ((this.altura / 100) ** 2)).toFixed(2);
            }
            return null;
        },
        // Motivos de inaptidão
        motivosInaptidao() {
            const motivos = [];
            if (this.idade && this.idade < 16) {
                motivos.push('- Só é permitido doar sangue com 16 anos ou mais;')
            }
            if (this.idade >= 16 && this.idade < 18 && this.autorizacao === 'nao') {
                motivos.push('- Jovens de 16 a 17 anos precisam de autorização dos pais ou responsáveis para doar;')
            }
            if (this.peso && this.peso < 50) {
                motivos.push('- É necessário ter 50kg ou mais para doar sangue;')
            }
            if (this.imc > 50) {
                motivos.push('- Superobesos (IMC > 50) não podem doar;')
            }
            if (this.imc > 40 && this.imc <= 50 && this.exame === 'nao') {
                motivos.push('- Pessoas com obesidade severa (40 < IMC < 50) só podem doar se tiverem uma avaliação médica recente com exames laboratoriais nos últimos 6 meses;')
            }
            if (this.genero === 'feminino' && this.doador === 'sim' && this.doacaoMulherRecente === 'sim') {
                motivos.push('- Mulheres precisam respeitar um intervalo de 90 dias após a última doação para doar novamente;')
            }
            if (this.genero === 'masculino' && this.doador === 'sim' && this.doacaoHomemRecente === 'sim') {
                motivos.push('- Homens precisam respeitar um intervalo de 60 dias após a última doação para doar novamente;')
            }
            if (this.sintomas === 'sim') {
                motivos.push('- É preciso passar 15 dias sem sintomas de doença para doar, para não correr risco de transmissão de doenças;')
            }
            if (this.alergia === 'sim') {
                motivos.push('- Não é permitido doar com sintomas de alergia, espere passar e poderá doar;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'polio_oral' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra o pólio oralmente (Sabin);')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'febre_tifoide_oral' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a febre tifóide oralmente;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'caxumba' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a caxumba (parotidite);')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'febre_amarela' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a febre amarela;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'sarampo' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra o sarampo;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'bcg' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar tomar a vacina BCG;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'rubeola' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a rubéola;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'varicela' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a varicela (catapora);')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'variola' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a varíola;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'dengue' && vacina.diasDesdeVacinacao < 30)) {
                motivos.push('- É preciso esperar 30 dias para doar após se vacinar contra a dengue;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'colera' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a cólera;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'polio' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra o pólio (Salk);')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'difteria' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a difteria;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'tetano' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra o tétano;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'febre_tifoife_injetavel' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar contra a febre tifóide com injeção;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'meningite' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a meningite;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'coqueluche' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra o coqueluche;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'hepatite_a' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a hepatite A;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'peste' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a peste;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'pneumococo' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra o pneumococo;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'leptospirose' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a leptospirose;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'brucelose' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a brucelose;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'hemophillus' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a hemophillus influenzae;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'hepatite_b_recombinante' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a hepatite B (recombinante);')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'covid' && vacina.coronavac === 'sim' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra o Covid-19 com a Coronavac ou a Covaxin;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'covid' && vacina.coronavac === 'nao' && vacina.diasDesdeVacinacao < 7)) {
                motivos.push('- É preciso esperar 7 dias para doar após se vacinar contra o Covid-19 com outras vacinas sem ser a Coronavac ou a Covaxin;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'hpv' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra o HPV;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'influenza' && vacina.diasDesdeVacinacao < 3)) {
                motivos.push('- É preciso esperar 48h para doar após se vacinar contra a gripe (influenza);')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'soro_antitetanico' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar com o soro antitetânico;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'antirrabica_profilatica' && vacina.diasDesdeVacinacao < 28)) {
                motivos.push('- É preciso esperar 4 semanas para doar após se vacinar com vacina antirrábica com o objetivo de profilaxia;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'antirrabica_apos_animal' && vacina.diasDesdeVacinacao < 365)) {
                motivos.push('- É preciso esperar 1 ano para doar após se vacinar com vacina antirrábica após exposição animal;')
            }
            if (this.vacinas.some(vacina => vacina.selected === 'hepatite_b_plasma' && vacina.diasDesdeVacinacao < 365)) {
                motivos.push('- É preciso esperar 1 ano para doar após se vacinar com vacina para hepatite B derivada de plasma;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'ajuste_aparelho' && procedimento.sangramento === 'nao' && procedimento.diasDesdeProcedimento < 2)) {
                motivos.push('- É preciso esperar 24h para doar sangue após fazer ajustes no aparelho ortodôntico;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'ajuste_aparelho' && procedimento.sangramento === 'sim' && procedimento.diasDesdeProcedimento < 4)) {
                motivos.push('- É preciso esperar 72h para doar sangue após fazer ajustes no aparelho ortodôntico quando há sangramento;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'carie_sem_anestesia' && procedimento.sangramento === 'nao' && procedimento.diasDesdeProcedimento < 2)) {
                motivos.push('- É preciso esperar 24h para doar sangue após remover uma cárie;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'carie_sem_anestesia' && procedimento.sangramento === 'sim' && procedimento.diasDesdeProcedimento < 4)) {
                motivos.push('- É preciso esperar 72h para doar sangue após remover uma cárie quando há sangramento;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'carie_com_anestesia' && procedimento.diasDesdeProcedimento < 3)) {
                motivos.push('- É preciso esperar 3 dias para doar sangue após remover uma cárie com anestesia local;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'drenagem_abscesso' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)) {
                motivos.push('- É preciso esperar 7 dias para doar sangue após uma drenagem de abscesso sem o uso de medicamentos;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_canal' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)) {
                motivos.push('- É preciso esperar 7 dias para doar sangue após um tratamento de canal sem o uso de medicamentos;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_gengivites' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)) {
                motivos.push('- É preciso esperar 7 dias para doar sangue após um tratamento de gengivites sem o uso de medicamentos;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'outras_cirurgias' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)) {
                motivos.push('- É preciso esperar 7 dias para doar sangue após realizar uma cirurgia ortodôntica sem o uso de medicamentos;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'implante_dentario' && procedimento.assintomaticosDental === 'nao')) {
                motivos.push('- É preciso estar assintomático após um implante dentário para poder doar sangue;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'implante_dentario' && procedimento.diasDesdeProcedimento < 30)) {
                motivos.push('- É preciso esperar 30 dias para doar após realizar um implante dentário;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_dentario' && procedimento.diasDesdeProcedimento < 30)) {
                motivos.push('- É preciso esperar 30 dias para doar após realizar um tratamento dentário com anestesia geral;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'remocao_tartaro' && procedimento.diasDesdeProcedimento < 3)) {
                motivos.push('- É preciso esperar 3 dias para doar sangue após um procedimento de remoção de tártaro;')
            }
            if (this.procedimentos.some(procedimento => procedimento.selected === 'outros_procedimentos' && procedimento.diasDesdeProcedimento < 3)) {
                motivos.push('- É preciso esperar 3 dias para doar sangue após realizar um procedimento dentário com anestesia local;')
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'amigdalectomia' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma amigdalectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'apendicectomia' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma apendicectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'artrodese_de_coluna' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma artrodese de coluna;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'artroscopia' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma artroscopia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cateterismo_cardiaco' && cirurgia.cirurgiaNormal === 'sim' && cirurgia.cirurgia30dias === 'sim')) {
                motivos.push('- É preciso esperar 30 dias para doar após fazer um cateterismo cardíaco com resultado normal;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cintilografia' && cirurgia.cirurgia7dias === 'sim')) {
                motivos.push('- É preciso esperar 7 dias para doar após fazer uma cintilografia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_adenoma_prostatico' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma cirurgia de adenoma prostático com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_de_paratireoide' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma cirurgia de paratireoide com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_de_tireoide' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma cirurgia de tireoide com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_dermatologicas_de_pequeno_porte' && cirurgia.cirurgiaAlta30dias === 'nao')) {
                motivos.push('- É preciso esperar 30 dias para doar após ter alta de uma cirurgia dermatológica de pequeno porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_endoscopicas' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer alguma cirurgia endoscopica;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurugias_ginecologicas_de_grande_porte' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer alguma cirurgia ginecológica de grande porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_ginecologicas_de_pequeno_porte' && cirurgia.cirurgiaAlta3meses === 'nao')) {
                motivos.push('- É preciso esperar 3 meses para doar após ter alta de uma cirurgia ginecológica de pequeno porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgia_de_malformacao_renal' && cirurgia.cirurgiaSequelaFuncional === 'nao' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma cirurgia de malformação renal, considerando que você não tenha ficado com sequelas funcionais;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_oftalmologicas_com_acesso_ao_snc' && cirurgia.cirurgiaSequela === 'nao' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma cirurgia oftalmológica com acesso ao SNC, considerando que você não tenha ficado com sequelas;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_oftalmologicas_de_pequeno_porte' && cirurgia.cirurgiaAlta30dias === 'nao')) {
                motivos.push('- É preciso esperar 30 dias para doar após ter alta de uma cirurgia oftalmológica de pequeno porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_ortopedicas' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer alguma cirurgia ortopédica;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_patalogicas_benignas_da_mama' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer alguma cirurgia patológica benigna da mama;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_plasticas_medio_porte' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer alguma cirurgia plástica de médio ou grande porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_plasticas_pequeno_porte' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer alguma cirurgia plástica de pequeno porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_urologicas_pequeno_porte' && cirurgia.cirurgiaAlta30dias === 'nao')) {
                motivos.push('- É preciso esperar 30 dias para doar após ter alta de uma cirurgia urológica de pequeno porte;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgia_varizes' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma cirurgia de varizes em MMII;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'colecistectomia' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma colecistectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'colectomia' && cirurgia.cirurgia12meses === 'sim')) {
                motivos.push('- É preciso esperar 12 meses para doar após fazer uma colectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'curetagem' && cirurgia.cirurgiaCuretagem === 'outro' && cirurgia.cirurgiaCura === 'nao')) {
                motivos.push('- Você precisa estar curado do motivo que fez você fazer a curetagem para poder doar sangue;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'curetagem' && cirurgia.cirurgiaCuretagem === 'aborto' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma curetagem pós-aborto;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'diu' && cirurgia.cirurgia48horas === 'sim')) {
                motivos.push('- É preciso esperar 48 horas para doar após colocar um diu;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'enxertos_heterologos_tecidos' && cirurgia.cirurgia12meses === 'sim')) {
                motivos.push('- É preciso esperar 12 meses para doar após fazer um enxerto heterólogo de tecidos;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'esclerose_de_varizes_de_membros_inferiores' && cirurgia.cirurgia3dias === 'sim')) {
                motivos.push('- É preciso esperar 3 dias para doar após fazer uma cirurgia para esclerose de varizes de membros inferiores;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'esplenectomia_pos-traumatica' && cirurgia.cirurgia12meses === 'sim')) {
                motivos.push('- É preciso esperar 12 meses para doar após fazer uma esplenectomia pós-traumática;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'exames_com_contrastes_aereos' && cirurgia.cirurgia30dias === 'sim')) {
                motivos.push('- É preciso esperar 30 dias para doar após fazer um exame com contraste aéreo;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'exames_com_contrastes_baritado' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia24horas === 'sim')) {
                motivos.push('- É preciso esperar 24 horas para doar após fazer um exame com contraste baritado com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'exames_com_contrastes_iodado_ou_gadolinio' && cirurgia.cirurgia30dias === 'sim')) {
                motivos.push('- É preciso esperar 30 dias para doar após fazer um exame com contrastes iodado ou gadolinio;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'extracao_calculos' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma extração de cálculos;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'gastrectomia_vertical' && cirurgia.cirurgia2anos === 'sim')) {
                motivos.push('- É preciso esperar 2 anos para doar após fazer uma gastrectomia vertical ou sleeve gástrico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'gastrectomia_vertical' && cirurgia.cirurgia2anos === 'nao' && (cirurgia.cirurgiaMedicamento === 'sim' || cirurgia.cirurgiaExame === 'nao'))) {
                motivos.push('- Mesmo após ter passado dois anos da cirurgia de gastrectomia vertical ou sleeve gástrico é preciso ter feito exames laboratóriais nos últimos 3 meses e não tomar medicamentos devido à cirurgia para poder doar sangue;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'hemorroidectomia' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma hemorroidectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'hepatectomia' && cirurgia.cirurgia12meses === 'sim')) {
                motivos.push('- É preciso esperar 12 meses para doar após fazer uma hepatectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'hernioplastia' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma hernioplastia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'infiltracao_articular' && cirurgia.cirurgia15dias === 'sim')) {
                motivos.push('- É preciso esperar 15 dias para doar após fazer uma infiltração articular;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'laminectomia' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma laminectomia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'laparoscopia' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma laparoscopia com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'laparotomia_branca' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma laparotomia branca com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'lipoaspiracao' && cirurgia.cirurgia6meses === 'sim')) {
                motivos.push('- É preciso esperar 6 meses para doar após fazer uma lipoaspiração;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'litotripsia' && cirurgia.cirurgia60dias === 'sim')) {
                motivos.push('- É preciso esperar 60 dias para doar após fazer uma litotripsia (a laser);');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'mielografia' && cirurgia.cirurgia30dias === 'sim')) {
                motivos.push('- É preciso esperar 30 dias para doar após fazer uma mielografia;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'nefrectomia_pos-trauma' && cirurgia.cirurgiaLaudo === 'sim' && cirurgia.cirurgia12meses === 'sim')) {
                motivos.push('- É preciso esperar 12 meses para doar após fazer uma nefrectomia (pós-trauma, doação, malformação, pós infecção) com laudo médico;');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'pleurostomia' && cirurgia.cirurgia3meses === 'sim')) {
                motivos.push('- É preciso esperar 3 meses para doar após fazer uma pleurostomia;');
            }
            if (this.remedios.some(remedio => remedio.selected === 'anti-inflamatorio' && remedio.remedio7dias === 'sim')) {
                motivos.push('- É preciso esperar 7 dias para doar após a suspensão do anti-inflamatório;');
            }
            if (this.remedios.some(remedio => remedio.selected === 'antibiotico' && remedio.remedioAntibioticoPenicilina === 'nao' && remedio.remedio15dias === 'sim')) {
                motivos.push('- É preciso esperar 15 dias para doar após a suspensão do antibiótico;');
            }
            if (this.remedios.some(remedio => remedio.selected === 'antibiotico' && remedio.remedioAntibioticoPenicilina === 'sim' && remedio.remedio30dias === 'sim')) {
                motivos.push('- É preciso esperar 30 dias para doar após a suspensão da Penicilina Benzatina;');
            }
            if (this.tatuagem === 'sim' && this.tatuagemRegistro === 'nao') {
                motivos.push('- É preciso esperar 12 meses para doar sangue após se tatuar. Caso você tenha a foto do registro do estúdio na Vigilância Sanitária e a nota fiscal/recibo do estabelecimento com a comprovação da data do procedimento no local é possível reduzir para 6 meses;')
            }
            if (this.tatuagem === 'sim' && this.tatuagemRegistro === 'sim' && this.tatuagem6 === 'sim') {
                motivos.push('- É preciso esperar 6 meses para doar sangue após se tatuar, considerando que você tenha a foto do registro do estúdio na Vigilância Sanitária e a nota fiscal/recibo do estabelecimento com a comprovação da data do procedimento no local, se não o tempo é de 12 meses;')
            }
            if (this.botox === 'sim' && this.botoxEspecializado === 'nao') {
                motivos.push('- É preciso esperar 6 meses para doar após ter feito botox, eletrolise, carboxiterapia, mesoterapia, micropigmentação, preenchimento com colágeno e ácido hialurônico ou pelling, caso tenha feito em uma clínica não especializada;')
            }
            if (this.botox === 'sim' && this.botoxEspecializado === 'sim' && this.botox3 === 'sim') {
                motivos.push('- É preciso esperar 3 meses para doar após ter feito botox, eletrolise, carboxiterapia, mesoterapia, micropigmentação, preenchimento com colágeno e ácido hialurônico ou pelling em uma clínica especializada;')
            }
            if (this.acupuntura === 'sim' && this.acupunturaAutorizada === 'nao') {
                motivos.push('- É preciso esperar 12 meses para doar após ter feito uma sessão de acupuntura por profissionais sem autorização da Vigilância Sanitária;')
            }
            if (this.acupuntura === 'sim' && this.acupunturaAutorizada === 'sim' && this.acupuntura72 === 'sim') {
                motivos.push('- É preciso esperar 72 horas para doar após ter feito uma sessão de acupuntura por profissionais com autorização da Vigilância Sanitária;')
            }
            if (this.piercing === 'sim' && this.piercingRegistro === 'nao') {
                motivos.push('- É preciso esperar 12 meses para doar sangue após colocar um piercing. Caso você tenha comprovante com data, local onde foi realizado e licença da Vigilância Sanitária do estabelecimento é possível reduzir para 6 meses;')
            }
            if (this.piercing === 'sim' && this.piercingRegistro === 'sim' && this.piercing6 === 'sim') {
                motivos.push('- É preciso esperar 6 meses para doar sangue após colocar um piercing, considerando que você tenha a foto do registro do estúdio na Vigilância Sanitária e a nota fiscal/recibo do estabelecimento com a comprovação da data do procedimento no local, se não o tempo é de 12 meses;')
            }
            if (this.piercingCavidade === 'agora') {
                motivos.push('- Enquanto você tiver um piercing em cavidade oral ou em um órgão genital você será considerado inapto para doar sangue;')
            }
            if (this.piercingCavidade === 'recente' && this.piercingRetirado === 'sim') {
                motivos.push('- É preciso esperar 12 meses para doar sangue após retirar um piercing localizado em cavidade oral ou em um órgão genital;')
            }
            if (this.endoscopia === 'sim') {
                motivos.push('- É preciso esperar 6 meses para doar após ter feito um exame ou procedimento endoscópico;')
            }
            if (this.soro === 'sim' && this.soroTipo === 'humano') {
                motivos.push('- É preciso esperar 12 meses para doar após o uso de imunoterapia passiva homóloga (soro humano);')
            }
            if (this.soro === 'sim' && this.soroTipo === 'normal' && this.soro4Semanas === 'sim') {
                motivos.push('- É preciso esperar 4 semanas para doar após o uso de imunoterapia passiva heteróloga (soro);')
            }
            if (this.drogaAnabolizante === 'sim') {
                motivos.push('- Há uma inaptidão de 12 meses após o uso de anabolizantes injetáveis;')
            }
            if (this.drogaAyahuasca === 'sim') {
                motivos.push('- Há uma inaptidão de 30 dias após o uso de Ayahuasca (Santo-Daime);')
            }
            if (this.drogaGerais === 'sim') {
                motivos.push('- Há uma inaptidão de 12 meses após o uso de drogas inaláveis, sintéticas ou alucionógenos;')
            }
            if (this.sexo === 'sim' && this.sexo3Parcerias === 'sim') {
                motivos.push('- Devido ao risco acrescido de DST não é permitido doar se você tiver tido mais de 3 parcerias sexuais nos últimos 6 meses;')
            }
            if (this.sexo === 'sim' && this.sexoCasual === 'sim') {
                motivos.push('- Devido ao risco acrescido de DST não é permitido doar se você tiver feito sexo casual com desconhecidos ou profissional do sexo nos últimos 12 meses;')
            }
            if (this.sexo === 'sim' && this.sexoSimultaneo === 'sim') {
                motivos.push('- Devido ao risco acrescido de DST não é permitido doar se você tiver feito sexo com mais de uma parceria simultaneamente nos últimos 12 meses;')
            }
            if (this.viagemMalaria === 'sim') {
                motivos.push('- Devido ao risco acrescido da tranmissão de Malária é necessário esperar 30 dias para doar após ter viajado para uma área endêmica;')
            }
            if (this.genero === 'feminino' && this.gravidez === 'agora') {
                motivos.push('- Não é permitido doar sangue durante a gravidez;')
            }
            if (this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'normal' && this.partoRecente === 'sim') {
                motivos.push('- Após ter um parto normal é preciso aguardar 3 meses para doar sangue;')
            }
            if (this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'cesariana' && this.cesarianaRecente === 'sim') {
                motivos.push('- Após ter uma cesariana é preciso aguardar 6 meses para doar sangue;')
            }
            if (this.genero === 'feminino' && this.gravidez === 'recente' && this.amamentacao === 'sim') {
                motivos.push('- Não é permitido doar sangue enquanto estiver amamentando ou até a criança completar 1 ano;')
            }
            if (this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'aborto' && this.abortoRecente === 'sim') {
                motivos.push('- Após ter um aborto é preciso aguardar 3 meses para doar sangue;')
            }

            return motivos;
        },
        // Motivos de inaptidão definitiva
        motivosInaptidaoDefinitiva() {
            const motivosD = [];
            if (this.idade > 60 && this.idade < 70 && this.doador === 'nao') {
                motivosD.push('- Pessoas com idade entre 60 e 69 só podem doar sangue caso já tenham doado anteriormente')
            }
            if (this.idade >= 70) {
                motivosD.push('- Pessoas com 70 anos ou mais não podem doar sangue');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_vasculares_complexas')) {
                motivosD.push('- Já ter feito uma cirurgia vascular complexa é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_cardiacas')) {
                motivosD.push('- Já ter feito uma cirurgia cardíaca é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_de_hipofise')) {
                motivosD.push('- Já ter feito uma cirurgia de hipófise é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'cirurgias_de_suprarrenal')) {
                motivosD.push('- Já ter feito uma cirurgia de suprarrenal é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'enterectomia')) {
                motivosD.push('- Já ter feito uma enterectomia é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'esplenectomia')) {
                motivosD.push('- Já ter feito uma esplenectomia é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'gastrectomia_total_e_subtotal')) {
                motivosD.push('- Já ter feito uma gastrectomia total e subtotal (incluindo cirurgia bariátrica e/ou colocação do anel) e gastroplastia é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'lobectomia')) {
                motivosD.push('- Já ter feito uma lobectomia é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'nefrectomia_por_patologias')) {
                motivosD.push('- Já ter feito uma nefrectomia por patalogias é causa de inaptidão definitiva');
            }
            if (this.cirurgias.some(cirurgia => cirurgia.selected === 'pneumectomia')) {
                motivosD.push('- Já ter feito uma pneumectomia é causa de inaptidão definitiva');
            }
            if (this.drogaBebida === 'sim' && this.drogaAlcolatra === 'sim') {
                motivosD.push('- Quem sofre de alcoolismo crônico é inapto definitivo para a doação de sangue');
            }
            if (this.drogaInjetavel === 'sim') {
                motivosD.push('- Quem já usou drogas ilegais injatáveis é inapto definitivo para a doação de sangue');
            }
            if (this.genero === 'feminino' && this.abortoRepeticao === 'sim') {
                motivosD.push('- Já ter tido 3 ou mais abortos é classificado como aborto de repetição e causa inaptidão definitiva para a doação de sangue');
            }
            if (this.viagemReinoUnido === 'sim') {
                motivosD.push('- Devido ao risco acrescido de transmissão de doenças, quem morou por mais de 3 meses no Reino Unido entre 1980 e 1996 é inapto definitivo');
            }
            if (this.viagemEuropa === 'sim') {
                motivosD.push('- Devido ao risco acrescido de transmissão de doenças, quem morou por mais de 5 anos na Europa após 1980 é inapto definitivo');
            }

            return motivosD;
        },
    },
    methods: {
        // Avança para o próximo step
        nextStep() {
            this.errorMessage = '';
            if (this.motivosInaptidaoDefinitiva.length > 0) {
                this.step = this.numberSteps;
            } else if (this.step < this.numberSteps) {
                this.step++;
            }
        },
        // Volta para o step anterior
        prevStep() {
            this.errorMessage = '';
            if (this.step > 1) {
                this.step--;
            }
        },
        // Formata a altura para não ter vírgula e ser calculada no IMC
        formatarAltura(valor) {
            const alturaSemVirgula = valor.replace(/[^0-9]/g, '');
            this.altura = alturaSemVirgula;
        },
        // Adiona um novo select de vacinas
        addVaccine() {
            this.vacinas.push({ selected: null, diaVacina: '', coronavac: null })
        },
        // Calcula os dias desde a vacina selecionada
        daysFromVaccination(index) {
            if (this.vacinas[index].diaVacina) {
                const dataVacina = new Date(this.vacinas[index].diaVacina);
                const dataAtual = new Date();
                const diferencaEmMilissegundos = dataAtual - dataVacina;
                const dias = Math.floor(diferencaEmMilissegundos / (1000 * 60 * 60 * 24));
                this.vacinas[index].diasDesdeVacinacao = dias;
            } else {
                this.vacinas[index].diasDesdeVacinacao = null;
            }
        },
        // Adiona um novo select de procedimentos odontológicos
        addProcedure() {
            this.procedimentos.push({ selected: null, diaOdontologico: '', sangramento: null, medicacaoDental: null, assintomaticosDental: null });
        },
        // Calcula os dias desde o procedimento odontológico selecionado
        daysFromDentalProcedure(index) {
            if (this.procedimentos[index].diaOdontologico) {
                const dataOdontologico = new Date(this.procedimentos[index].diaOdontologico);
                const dataAtual = new Date();
                const diferencaEmMilissegundos = dataAtual - dataOdontologico;
                const dias = Math.floor(diferencaEmMilissegundos / (1000 * 60 * 60 * 24));
                this.procedimentos[index].diasDesdeProcedimento = dias;
            } else {
                this.procedimentos[index].diasDesdeProcedimento = null;
            }
        },
        // Adiona um novo select de cirurgias
        addSurgery() {
            this.cirurgias.push({ selected: null, cirurgia48horas: '', cirurgia3dias: '', cirurgia7dias: '', cirurgia15dias: '', cirurgia30dias: '', cirurgia60dias: '', cirurgia3meses: '', cirurgia6meses: '', cirurgia12meses: '' });
        },
        // Adiona um novo select de doenças
        addDiseases() {
            this.doencas.push({ selected: null });
        },
        // Adiona um novo select de remedios
        addMedications() {
            this.remedios.push({ selected: null });
        },
        // Ao clicar na tecla enter avança para o próximo step
        handleKeyPress(event) {
            if (event.key === 'Enter') {
                this.nextStep();
            }
        }
    },
    mounted() {
        window.addEventListener('keydown', this.handleKeyPress);
    },
    beforeUnmount() {
        window.removeEventListener('keydown', this.handleKeyPress);
    }
};
</script>


<style scoped>
/* Estilos da barra de progresso */

.progress-bar-container {
    position: relative;
    width: 100%;
    height: 5px;
    background-color: var(--cinza-claro);
    border-radius: 2.5px;
    margin-bottom: 40px;
}

.progress-bar {
    height: 100%;
    background-color: var(--vermelho);
    border-radius: 5px;
    transition: 0.3s ease-in-out;
}

.progress-steps {
    position: absolute;
    top: -5px;
    width: 100%;
    display: flex;
    justify-content: space-between;
}

.progress-step {
    width: 15px;
    height: 15px;
    background-color: var(--cinza-claro);
    border-radius: 50%;
    transition: background-color 0.3s ease-in-out;
}

.progress-step.active {
    background-color: var(--vermelho);
}


/* Estilos dos textos */
h2 {
    color: var(--preto);
    font-size: 1.4em;
    margin: 10px 0 30px;
    font-weight: normal;
    text-align: center;
}

h3 {
    color: var(--preto);
    font-size: 1.25em;
    margin: -10px 20px 25px;
    font-weight: normal;
    text-align: center;
}

h4 {
    color: var(--preto);
    font-size: 1.1em;
    margin-bottom: 10px;
    font-weight: normal;
}


/* Estilo dos inputs */

.input-container {
    position: relative;
    width: 100%;
}

.input-container::after {
    position: absolute;
    right: 35px;
    top: 50%;
    transform: translateY(-50%);
    color: var(--cinza-escuro);
}

#input-peso::after {
    content: 'kg';
}

#input-altura::after {
    content: 'm';
}

#input-idade::after {
    content: 'anos';
}

input[type="number"],
input[type="text"],
select {
    display: block;
    width: 100%;
    box-sizing: border-box;
    border: 1px solid var(--cinza-medio);
    border-radius: 5px;
    color: var(--cinza-escuro);
    padding: 6px 10px;
    margin-bottom: 10px;
}

input[type="number"]:active select:active,
input[type="text"]:active select:active {
    border: 1px solid var(--cinza-medio);
}

.vue-select {
    margin-bottom: 25px;
}

.add_item {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 20px;
}

input[type="date"] {
    font-size: 16px;
    padding: 5px;
    border-radius: 3px;
    background-color: var(--cinza-claro);
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

@media only screen and (max-width: 500px) {
    input[type="date"]:not(.has-value):before {
        color: var(--preto);
        content: attr(placeholder);
    }
}


/* Estilo dos radio buttons */
.radio-group,
.radio-group-small {
    display: flex;
    margin-bottom: 25px;
}

.radio-group label,
.radio-group-small label {
    cursor: pointer;
    text-align: center;
    border: 1px solid var(--cinza-medio);
    transition: background-color 0.3s ease, border-color 0.3s ease;
}

.radio-group label {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 50%;
    padding: 10px;
}

.radio-group-small label {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 15%;
    padding: 5px 10px;
}


.radio-group label:first-child,
.radio-group-small label:first-child {
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
}

.radio-group label:last-child,
.radio-group-small label:last-child {
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
}

.radio-group input[type="radio"],
.radio-group-small input[type="radio"] {
    display: none;
    margin: 0 10px;
}

.radio-group label.checked,
.radio-group-small label.checked {
    background-color: var(--vermelho);
    color: var(--branco);
    border-color: var(--vermelho);
}


/* Estilos dos botões */

.navigation-buttons {
    display: flex;
    flex-direction: row;
    align-items: end;
    justify-content: space-between;
    margin-top: 15px;
}

.button_prev,
.button_next,
.button_finish {
    border-radius: 5px;
    padding: 8px 10px;
}

.button_prev {
    background-color: var(--cinza-claro);
    color: var(--preto);
}

.button_next {
    background-color: var(--vermelho);
    color: var(--branco);
    margin-left: auto;
}

.button_finish {
    background-color: var(--verde);
    color: var(--branco);
}

.button_add {
    background-color: var(--cinza-claro);
    border-radius: 3px;
    padding: 8px 5px;
}


/* Estilo dos icons de informação */

.info {
    display: flex;
    position: relative;
}

.info-h4 {
    max-width: 375px;
}

.info-icon {
    background-color: var(--azul);
    color: var(--branco);
    border-radius: 50%;
    width: 20px;
    height: 20px;
    margin-left: 15px;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 14px;
    cursor: pointer;
}

.info-popup {
    position: absolute;
    top: 30px;
    right: 0;
    background-color: var(--branco);
    border: 1px solid var(--cinza-medio);
    border-radius: 4px;
    padding: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    z-index: 10;
    width: 200px;
}
</style>