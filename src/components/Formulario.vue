<template>
    <form @submit.prevent="submitForm">
        <div>
            <!-- Barra de progresso -->
            <div v-if="step < 15" class="progress-bar-container">
                <div class="progress-bar" :style="{ width: progress + '%' }"></div>
                <div class="progress-steps">
                    <div v-for="n in 14" :key="n" class="progress-step" :class="{ 'active': step >= n }"></div>
                </div>
            </div>

            <!-- Step 1: Gênero -->
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
            </div>

            <!-- Step 2: Idade -->
            <div v-if="step === 2">
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

            <!-- Step 3: Peso e altura -->
            <div v-if="step === 3">
                <h4>Qual o seu peso?</h4>
                <div class="input-container" id="input-peso">
                    <input type="number" required v-model="peso">
                </div>
                <h4>Qual a sua altura?</h4>
                <div class="input-container" id="input-altura">
                    <input type="number" required v-model="altura">
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

            <!-- Step 4: Doação -->
            <div v-if="step === 4">
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
                <div v-if="doador === 'sim'">
                    <h4>Quando foi a data da sua última doação?</h4>
                    <input type="date" v-model="ultimaDoacao" @change="daysFromLastDonation">
                </div>
            </div>

            <!-- Step 5: Sintomas gerais -->
            <div v-if="step === 5">
                <h4>Você sentiu algum sintoma de doença como: febre, vômitos, tonturas, moleza no corpo... nos últimos
                    15
                    dias?</h4>
                <div class="radio-group">
                    <label :class="{ 'checked': sintomas === 'sim' }">
                        <input type="radio" value="sim" v-model="sintomas">
                        Sim
                    </label>
                    <label :class="{ 'checked': sintomas === 'nao' }">
                        <input type="radio" value="nao" v-model="sintomas">
                        Não
                    </label>
                </div>
            </div>

            <!-- Step 6: Alergia -->
            <div v-if="step === 6">
                <h4>Você está com algum sintoma de alergia?</h4>
                <div class="radio-group">
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

            <!-- Step 7: Vacinação -->
            <div v-if="step === 7">
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
                    <h4>Selecione as vacinas que você tomou:</h4>
                    <div v-for="(vacina, index) in vacinas" :key="index">
                        <select v-model="vacina.selected">
                            <option v-for="option in listaVacinas" :key="option.value" :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div class="add_item">
                            <input type="date" v-model="vacina.diaVacina" @change="daysFromVaccination(index)">
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

            <!-- Step 8: Cirurgias e procedimentos odontológicos -->
            <div v-if="step === 8">
                <h4>Você realizou algum procedimento ou cirurgia odontológica nos últimos 30 dias?</h4>
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
                    <h4>Selecione os procedimentos que você realizou:</h4>
                    <div v-for="(procedimento, index) in procedimentos" :key="index">
                        <select v-model="procedimento.selected">
                            <option v-for="option in listaProcedimentosOdontologicos" :key="option.value"
                                :value="option.value">
                                {{ option.text }}
                            </option>
                        </select>
                        <div class="add_item">
                            <input type="date" v-model="procedimento.diaOdontologico"
                                @change="daysFromDentalProcedure(index)">
                            <button v-if="index === procedimentos.length - 1" @click="addProcedure"
                                class="button_add">Adicionar outro procedimento</button>
                        </div>
                        <div
                            v-if="procedimento.selected === 'ajuste_aparelho' || procedimento.selected === 'carie_sem_anestesia'">
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
                            v-if="procedimento.selected === 'drenagem_abscesso' || procedimento.selected === 'tratamento_canal' || procedimento.selected === 'tratamento_gengivites' || procedimento.selected === 'outras_cirurgias'">
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

            <!-- Step 9: Procedimentos terapêuticos e estéticos invasivos -->
            <div v-if="step === 9">
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
                <h4>Você fez botox, eletrolise, carboxiterapia, mesoterapia, micropigmentação, preenchimento com colágeno e ácido, hialurônico ou pelling nos últimos 6 meses?</h4>
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

            <!-- Step 11: Drogas lícitas ou ilícitas -->
            <div v-if="step === 11">
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
                <h4>Você fez uso de drogas inaláveis, sintéticas ou alucionógenos nos últimos 12 meses?</h4>
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

            <!-- Step 12: Vida sexual -->
            <div v-if="step === 12">
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

            <!-- Step 13: Risco acrescido para transmissão de doenças devido à viagens -->
            <div v-if="step === 13">
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

            <!-- Step 14: condição ginecológica da mulher -->
            <div v-if="step === 14">
                <div v-if="genero === 'feminino'">
                    <h4>Você está grávida ou esteve grávida nos últimos 12 meses?</h4>
                    <div class="radio-group">
                        <label :class="{ 'checked': gravidez === 'agora' }">
                            <input type="radio" value="agora" v-model="gravidez">
                            Estou grávida
                        </label>
                        <label :class="{ 'checked': gravidez === 'recente' }">
                            <input type="radio" value="recente" v-model="gravidez">
                            Estive recentemente
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

            <!-- Step 15: Resultado -->
            <div v-if="step === 15">
                <div v-if="inaptidaoDefinitiva">
                    <h2>Você é <strong>inapto definitivo</strong> para doar sangue</h2>
                    <h4>Motivo(s):</h4>
                    <p v-if="this.idade >= 70">- Pessoas com 70 anos ou mais não podem doar sangue</p>
                    <p v-if="this.drogaBebida === 'sim' && this.drogaAlcolatra === 'sim'">- Quem sofre de alcoolismo
                        crônico é inapto definitivo para a doação de sangue</p>
                    <p v-if="this.drogaInjetavel === 'sim'">- Quem já usou drogas ilegais injatáveis é inapto definitivo
                        para a doação de sangue</p>
                    <p v-if="this.genero === 'feminino' && this.abortoRepeticao === 'sim'">- Já ter tido 3 ou mais
                        abortos é classificado como aborto de repetição e causa inaptidão definitiva para a doação de
                        sangue</p>
                    <p v-if="this.viagemReinoUnido === 'sim'">- Devido ao risco acrescido de transmissão de doenças,
                        quem morou por mais de 3 meses no Reino Unido entre 1980 e 1996 é inapto definitivo</p>
                    <p v-if="this.viagemEuropa === 'sim'">- Devido ao risco acrescido de transmissão de doenças,
                        quem morou por mais de 5 anos na Europa após 1980 é inapto definitivo</p>
                </div>
                <div v-else-if="inapto">
                    <h2>Você está <strong>inapto</strong> para doar</h2>
                    <h4>Motivo(s):</h4>
                    <p v-if="this.idade < 16">- Só é permitido doar sangue com 16 anos ou mais</p>
                    <p v-if="this.idade >= 16 && this.idade < 18 && this.autorizacao === 'nao'">- Jovens de 16 a 18 anos
                        precisam de autorização dos pais ou responsáveis para doar</p>
                    <p v-if="this.idade > 60 && this.idade < 70 && this.doador === 'nao'">- Pessoas com idade entre 60 e
                        69 só podem doar sangue caso já tenham doado anteriormente</p>
                    <p v-if="this.peso < 50">- É necessário ter mais de 50kg para doar sangue</p>
                    <p v-if="this.imc > 50">- Superobesos (IMC > 50) não podem doar</p>
                    <p v-if="this.imc > 40 && this.imc <= 50 && this.exame === 'nao'">- Pessoas com obesidade severa
                        (IMC > 40) só podem doar se tiverem uma avaliação médica recente com exames laboratoriais nos
                        últimos 6 meses </p>
                    <p v-if="this.genero === 'feminino' && this.doador === 'sim' && this.diasDesdeUltimaDoacao < 90">-
                        Mulheres precisam respeitar um intervalo de 90 dias após a última doação para doar novamente</p>
                    <p v-if="this.genero === 'masculino' && this.doador === 'sim' && this.diasDesdeUltimaDoacao < 60">-
                        Homens precisam respeitar um intervalo de 60 dias após a última doação para doar novamente</p>
                    <p v-if="this.sintomas === 'sim'">- É preciso passar 15 dias sem sintomas de doença para doar, para
                        não correr risco de transmissão de doenças</p>
                    <p v-if="this.alergia === 'sim'">- Não é permitido doar com sintomas de alergia, espere passar e
                        poderá doar</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'polio_oral' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra o pólio oralmente (Sabin)</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'febre_tifoide_oral' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a febre tifóide oralmente</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'caxumba' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a caxumba (parotidite)</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'febre_amarela' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a febre amarela</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'sarampo' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra o sarampo</p>
                    <p v-if="this.vacinas.some(vacina => vacina.selected === 'bcg' && vacina.diasDesdeVacinacao < 28)">-
                        É preciso esperar 4 semanas para doar tomar a vacina BCG</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'rubeola' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a rubéola</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'varicela' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a varicela (catapora)</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'variola' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a varíola</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'dengue' && vacina.diasDesdeVacinacao < 30)">
                        - É preciso esperar 30 dias para doar após se vacinar contra a dengue</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'colera' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a cólera</p>
                    <p v-if="this.vacinas.some(vacina => vacina.selected === 'polio' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra o pólio (Salk)</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'difteria' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a difteria</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'tetano' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra o tétano</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'febre_tifoife_injetavel' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 4 semanas para doar após se vacinar contra a febre tifóide com injeção</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'meningite' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a meningite</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'coqueluche' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra o coqueluche</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'hepatite_a' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a hepatite A</p>
                    <p v-if="this.vacinas.some(vacina => vacina.selected === 'peste' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a peste</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'pneumococo' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra o pneumococo</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'leptospirose' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a leptospirose</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'brucelose' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a brucelose</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'hemophillus' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a hemophillus influenzae</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'hepatite_b_recombinante' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a hepatite B (recombinante)</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'covid' && vacina.coronavac === 'sim' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra o Covid-19 com a Coronavac ou a Covaxin
                    </p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'covid' && vacina.coronavac === 'nao' && vacina.diasDesdeVacinacao < 7)">
                        - É preciso esperar 7 dias para doar após se vacinar contra o Covid-19 com outras vacinas sem
                        ser a Coronavac ou a Covaxin</p>
                    <p v-if="this.vacinas.some(vacina => vacina.selected === 'hpv' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra o HPV</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'influenza' && vacina.diasDesdeVacinacao < 3)">
                        - É preciso esperar 48h para doar após se vacinar contra a gripe (influenza)</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'soro_antitetanico' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar com o soro antitetânico</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'antirrabica_profilatica' && vacina.diasDesdeVacinacao < 28)">
                        - É preciso esperar 4 semanas para doar após se vacinar com vacina antirrábica com o objetivo de
                        profilaxia</p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'antirrabica_apos_animal' && vacina.diasDesdeVacinacao < 365)">
                        - É preciso esperar 1 ano para doar após se vacinar com vacina antirrábica após exposição animal
                    </p>
                    <p
                        v-if="this.vacinas.some(vacina => vacina.selected === 'hepatite_b_plasma' && vacina.diasDesdeVacinacao < 365)">
                        - É preciso esperar 1 ano para doar após se vacinar com vacina para hepatite B derivada de
                        plasma</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'ajuste_aparelho' && procedimento.sangramento === 'nao' && procedimento.diasDesdeProcedimento < 2)">
                        - É preciso esperar 24h para doar sangue após fazer ajustes no aparelho ortodôntico</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'ajuste_aparelho' && procedimento.sangramento === 'sim' && procedimento.diasDesdeProcedimento < 4)">
                        - É preciso esperar 72h para doar sangue após fazer ajustes no aparelho ortodôntico quando há
                        sangramento
                    </p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'carie_sem_anestesia' && procedimento.sangramento === 'nao' && procedimento.diasDesdeProcedimento < 2)">
                        - É preciso esperar 24h para doar sangue após remover uma cárie</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'carie_sem_anestesia' && procedimento.sangramento === 'sim' && procedimento.diasDesdeProcedimento < 4)">
                        - É preciso esperaro 72h para doar sangue após remover uma cárie quando há sangramento</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'carie_com_anestesia' && procedimento.diasDesdeProcedimento < 3)">
                        - É preciso esperaro 3 dias para doar sangue após remover uma cárie com anestesia local</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'drenagem_abscesso' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)">
                        - É preciso esperar 7 dias para doar sangue após uma drenagem de abscesso sem o uso de
                        medicamentos</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_canal' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)">
                        - É preciso esperar 7 dias para doar sangue após um tratamento de canal sem o uso de
                        medicamentos</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_gengivites' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)">
                        - É preciso esperar 7 dias para doar sangue após um tratamento de gengivites sem o uso de
                        medicamentos</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'outras_cirurgias' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7)">
                        - É preciso esperar 7 dias para doar sangue após realizar uma cirurgia ortodôntica sem o uso de
                        medicamentos
                    </p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'implante_dentario' && procedimento.assintomaticosDental === 'nao')">
                        - É preciso estar assintomático após um implante dentário para poder doar sangue</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'implante_dentario' && procedimento.diasDesdeProcedimento < 30)">
                        - É preciso esperar 30 dias para doar após realizar um implante dentário</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_dentario' && procedimento.diasDesdeProcedimento < 30)">
                        - É preciso esperar 30 dias para doar após realizar um tratamento dentário com anestesia geral
                    </p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'remocao_tartaro' && procedimento.diasDesdeProcedimento < 3)">
                        - É preciso esperar 3 dias para doar sangue após um procedimento de remoção de tártaro</p>
                    <p
                        v-if="this.procedimentos.some(procedimento => procedimento.selected === 'outros_procedimentos' && procedimento.diasDesdeProcedimento < 3)">
                        - É preciso esperar 3 dias para doar sangue após realizar um procedimento dentário com anestesia
                        local</p>
                    <p v-if="this.tatuagem === 'sim' && this.tatuagemRegistro === 'nao'">
                        - É preciso esperar 12 meses para doar sangue após se tatuar, fazer micropigmentação ou
                        maquiagem definitiva. Caso a pessoa tenha a foto do registro do estúdio na Vigilância Sanitária
                        e a nota fiscal/recibo do estabelecimento com a comprovação da data do
                        procedimento no local é possível reduzir para 6 meses</p>
                    <p v-if="this.tatuagem === 'sim' && this.tatuagemRegistro === 'sim' && this.tatuagem6 === 'sim'">
                        - É preciso esperar no mínimo 6 meses para doar sangue após se tatuar, fazer micropigmentação ou
                        maquiagem definitiva, considerando que a pessoa tenha a foto do registro do estúdio na
                        Vigilância Sanitária e a nota fiscal/recibo do estabelecimento com a
                        comprovação da data do procedimento no local, se não o tempo é de 12 meses</p>
                    <p v-if="this.soro === 'sim' && this.soroTipo === 'humano'">- É preciso esperar 12 meses para doar
                        após o uso de imunoterapia passiva homóloga (soro humano)</p>
                    <p
                        v-if="soroNormal = this.soro === 'sim' && this.soroTipo === 'normal' && this.soro4Semanas === 'sim'">
                        - É preciso esperar 4 semanas para doar após o uso de imunoterapia passiva heteróloga (soro)</p>
                    <p v-if="this.drogaAnabolizante === 'sim'">- Há uma inaptidão de 12 meses após o uso de
                        anabolizantes injetáveis</p>
                    <p v-if="this.drogaAyahuasca === 'sim'">- Há uma inaptidão de 30 dias após o uso de Ayahuasca
                        (Santo-Daime)</p>
                    <p v-if="this.drogaGerais === 'sim'">- Há uma inaptidão de 12 meses após o uso de drogas inaláveis,
                        sintéticas ou alucionógenos</p>
                    <p v-if="this.sexo === 'sim' && this.sexo3Parcerias === 'sim'">- Devido ao risco acrescido de DST
                        não é permitido doar se você tiver tido mais de 3 parcerias sexuais nos últimos 6 meses</p>
                    <p v-if="this.sexo === 'sim' && this.sexoCasual === 'sim'">- Devido ao risco acrescido de DST não é
                        permitido doar se você tiver feito sexo casual com desconhecidos ou profissional do sexo nos
                        últimos 12 meses</p>
                    <p v-if="this.sexo === 'sim' && this.sexoSimultaneo === 'sim'">- Devido ao risco acrescido de DST
                        não é permitido doar se você tiver feito sexo com mais de uma parceria simultaneamente nos
                        últimos 12 meses</p>
                    <p v-if="this.viagemMalaria === 'sim'">- Devido ao risco acrescido da tranmissão de Malária é
                        necessário esperar 30 dias para doar após ter viajado para uma área endêmica</p>
                    <p v-if="this.genero === 'feminino' && this.gravidez === 'agora'">- Não é permitido doar sangue
                        durante a gravidez</p>
                    <p
                        v-if="this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'normal' && this.partoRecente === 'sim'">
                        - Após ter um parto normal é preciso aguardar 3 meses para doar sangue</p>
                    <p
                        v-if="this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'cesariana' && this.cesarianaRecente === 'sim'">
                        - Após ter uma cesariana é preciso aguardar 6 meses para doar sangue</p>
                    <p v-if="this.genero === 'feminino' && this.gravidez === 'recente' && this.amamentacao === 'sim'">-
                        Não é permitido doar sangue enquanto estiver amamentando ou até a criança completar 1 ano</p>
                    <p
                        v-if="this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'aborto' && this.abortoRecente === 'sim'">
                        - Após ter um aborto é preciso aguardar 3 meses para doar sangue</p>
                </div>
                <div v-else>
                    <h2>Você está <strong>apto</strong> para doar</h2>
                    <p v-if="this.idade >= 16 && this.idade < 18 && this.autorizacao === 'sim'">- Como você é menor de
                        idade, lembre-se de que para doar você precisa ter a permissão dos seus pais ou responsável
                        legal e estar na companhia de um deles</p>
                    <p v-if="this.drogaMaconha === 'sim'">- Você precisa passar 12 horas sem fumar maconha ou narguillé
                        para poder doar</p>
                    <p v-if="this.drogaCigarro === 'sim'">- Lembre-se de que você não deve fumar por duas horas após a
                        doação (incluindo cigarros eletrônicos)</p>
                    <p>- Lembre-se de estar bem alimentado e hidratado e de não ingerir bebida alcoólicas nas 12h que
                        antecedem a doação</p>
                </div>
            </div>
        </div>

        <!-- Navegação entre os steps -->
        <div class="navigation-buttons">
            <button class="button_prev" type="button" @click="prevStep" v-if="step > 1 && step < 15">Anterior</button>
            <button class="button_next" type="button" @click="nextStep" v-if="step < 14">Próximo</button>
            <button class="button_finish" type="button" @click="nextStep" v-if="step === 14">Finalizar</button>
        </div>
    </form>

    <!-- Debugging (apenas para verificar se os dados estão sendo atualizados corretamente) -->
    <!-- <p>Gênero: {{ genero }}</p>
    <p>Idade: {{ idade }}</p>
    <p>Autorização: {{ autorizacao }}</p>
    <p>Peso: {{ peso }}</p>
    <p>Altura: {{ altura }}</p>
    <p>Seu IMC: {{ imc }}</p>
    <p>Exames: {{ exame }}</p>
    <p>Doador: {{ doador }}, {{ diasDesdeUltimaDoacao }} dias</p>
    <p>Sintomas: {{ sintomas }}</p>
    <p>Procedimento odontológico: {{ odontologico }}</p>
    <ul>
      <li v-for="(procedimento, index) in procedimentos" :key="index">
        {{ procedimento.selected }} - {{ procedimento.diaOdontologico }} - {{ procedimento.diasDesdeProcedimento }} - {{
          procedimento.sangramento }}
      </li>
    </ul> -->
</template>

<script>
export default {
    name: 'Formulario',
    data() {
        return {
            step: 1,
            genero: '',
            idade: '',
            autorizacao: '',
            peso: '',
            altura: '',
            exame: '',
            doador: '',
            diasDesdeUltimaDoacao: '',
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
                { value: 'soro_antitetanico', text: 'Soro Antitetânico' },
                { value: 'sarampo', text: 'Sarampo' },
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
                sangramento: ''
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
            if (this.step < 15) {
                return ((this.step - 1) / 13) * 100;
            }
        },
        // Cálculo do IMC
        imc() {
            if (this.peso && this.altura) {
                return (this.peso / (this.altura ** 2)).toFixed(2);
            }
            return null;
        },
        // Motivos de inaptidão
        inapto() {
            const menorDe16 = this.idade < 16;
            const semAutorizacao = this.idade >= 16 && this.idade < 18 && this.autorizacao === 'nao';
            const maiorQue60SemDoacao = this.idade > 60 && this.idade < 70 && this.doador === 'nao';
            const pesoInadequado = this.peso < 50;
            const imcSuperobeso = this.imc > 50;
            const imcObesidadeSevera = this.imc > 40 && this.imc <= 50 && this.exame === 'nao';
            const ultimaDoacaoMulher = this.genero === 'feminino' && this.doador === 'sim' && this.diasDesdeUltimaDoacao < 90;
            const ultimaDoacaoHomem = this.genero === 'masculino' && this.doador === 'sim' && this.diasDesdeUltimaDoacao < 60;
            const sintomasDoenca = this.sintomas === 'sim';
            const reacaoAlergica = this.alergia === 'sim';
            const vacinaPolioOral = this.vacinas.some(vacina => vacina.selected === 'polio_oral' && vacina.diasDesdeVacinacao < 28);
            const vacinaFebreTifoideOral = this.vacinas.some(vacina => vacina.selected === 'febre_tifoide_oral' && vacina.diasDesdeVacinacao < 28);
            const vacinaCaxumba = this.vacinas.some(vacina => vacina.selected === 'caxumba' && vacina.diasDesdeVacinacao < 28);
            const vacinaFebreAmarela = this.vacinas.some(vacina => vacina.selected === 'febre_amarela' && vacina.diasDesdeVacinacao < 28);
            const vacinaSarampo = this.vacinas.some(vacina => vacina.selected === 'sarampo' && vacina.diasDesdeVacinacao < 28);
            const vacinaBCG = this.vacinas.some(vacina => vacina.selected === 'bcg' && vacina.diasDesdeVacinacao < 28);
            const vacinaRubeola = this.vacinas.some(vacina => vacina.selected === 'rubeola' && vacina.diasDesdeVacinacao < 28);
            const vacinaVaricela = this.vacinas.some(vacina => vacina.selected === 'varicela' && vacina.diasDesdeVacinacao < 28);
            const vacinaVariola = this.vacinas.some(vacina => vacina.selected === 'variola' && vacina.diasDesdeVacinacao < 28);
            const vacinaDengue = this.vacinas.some(vacina => vacina.selected === 'dengue' && vacina.diasDesdeVacinacao < 30);
            const vacinaColera = this.vacinas.some(vacina => vacina.selected === 'colera' && vacina.diasDesdeVacinacao < 3);
            const vacinaPolio = this.vacinas.some(vacina => vacina.selected === 'polio' && vacina.diasDesdeVacinacao < 3);
            const vacinaDifteria = this.vacinas.some(vacina => vacina.selected === 'difteria' && vacina.diasDesdeVacinacao < 3);
            const vacinaTetano = this.vacinas.some(vacina => vacina.selected === 'tetano' && vacina.diasDesdeVacinacao < 3);
            const vacinaFebreTifoideInjetavel = this.vacinas.some(vacina => vacina.selected === 'febre_tifoife_injetavel' && vacina.diasDesdeVacinacao < 3);
            const vacinaMeningite = this.vacinas.some(vacina => vacina.selected === 'meningite' && vacina.diasDesdeVacinacao < 3);
            const vacinaCoqueluche = this.vacinas.some(vacina => vacina.selected === 'coqueluche' && vacina.diasDesdeVacinacao < 3);
            const vacinaHepatiteA = this.vacinas.some(vacina => vacina.selected === 'hepatite_a' && vacina.diasDesdeVacinacao < 3);
            const vacinaPeste = this.vacinas.some(vacina => vacina.selected === 'peste' && vacina.diasDesdeVacinacao < 3);
            const vacinaPneumococo = this.vacinas.some(vacina => vacina.selected === 'pneumococo' && vacina.diasDesdeVacinacao < 3);
            const vacinaLeptospirose = this.vacinas.some(vacina => vacina.selected === 'leptospirose' && vacina.diasDesdeVacinacao < 3);
            const vacinaBrucelose = this.vacinas.some(vacina => vacina.selected === 'brucelose' && vacina.diasDesdeVacinacao < 3);
            const vacinaHemophillus = this.vacinas.some(vacina => vacina.selected === 'hemophillus' && vacina.diasDesdeVacinacao < 3);
            const vacinaHepatiteBRecombinante = this.vacinas.some(vacina => vacina.selected === 'hepatite_b_recombinante' && vacina.diasDesdeVacinacao < 3);
            const vacinaCovidCoronavac = this.vacinas.some(vacina => vacina.selected === 'covid' && vacina.coronavac === 'sim' && vacina.diasDesdeVacinacao < 3);
            const vacinaCovidOutras = this.vacinas.some(vacina => vacina.selected === 'covid' && vacina.coronavac === 'nao' && vacina.diasDesdeVacinacao < 7);
            const vacinaHPV = this.vacinas.some(vacina => vacina.selected === 'hpv' && vacina.diasDesdeVacinacao < 3);
            const vacinaInfluenza = this.vacinas.some(vacina => vacina.selected === 'influenza' && vacina.diasDesdeVacinacao < 3);
            const vacinaSoroAntitetanico = this.vacinas.some(vacina => vacina.selected === 'soro_antitetanico' && vacina.diasDesdeVacinacao < 28);
            const vacinaAntirrabicaProfilatica = this.vacinas.some(vacina => vacina.selected === 'antirrabica_profilatica' && vacina.diasDesdeVacinacao < 28);
            const vacinaAntirrabicaAposAnimal = this.vacinas.some(vacina => vacina.selected === 'antirrabica_apos_animal' && vacina.diasDesdeVacinacao < 365);
            const vacinaHepatiteBPlasma = this.vacinas.some(vacina => vacina.selected === 'hepatite_b_plasma' && vacina.diasDesdeVacinacao < 365);
            const ajusteAparelhoSemSangramento = this.procedimentos.some(procedimento => procedimento.selected === 'ajuste_aparelho' && procedimento.sangramento === 'nao' && procedimento.diasDesdeProcedimento < 2);
            const ajusteAparelhoComSangramento = this.procedimentos.some(procedimento => procedimento.selected === 'ajuste_aparelho' && procedimento.sangramento === 'sim' && procedimento.diasDesdeProcedimento < 4);
            const carieSemAnestesiaSemSangramento = this.procedimentos.some(procedimento => procedimento.selected === 'carie_sem_anestesia' && procedimento.sangramento === 'nao' && procedimento.diasDesdeProcedimento < 2);
            const carieSemAnestesiaComSangramento = this.procedimentos.some(procedimento => procedimento.selected === 'carie_sem_anestesia' && procedimento.sangramento === 'sim' && procedimento.diasDesdeProcedimento < 4);
            const carieComAnestesia = this.procedimentos.some(procedimento => procedimento.selected === 'carie_com_anestesia' && procedimento.diasDesdeProcedimento < 3);
            const drenagemAbscesso = this.procedimentos.some(procedimento => procedimento.selected === 'drenagem_abscesso' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7);
            const tratamentoCanal = this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_canal' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7);
            const tratementoGengivites = this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_gengivites' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7);
            const outrasCirurgias = this.procedimentos.some(procedimento => procedimento.selected === 'outras_cirurgias' && procedimento.medicacaoDental === 'nao' && procedimento.diasDesdeProcedimento < 7);
            const implanteDentario = this.procedimentos.some(procedimento => procedimento.selected === 'implante_dentario' && procedimento.assintomaticosDental === 'nao');
            const implanteDentarioAssintomatico = this.procedimentos.some(procedimento => procedimento.selected === 'implante_dentario' && procedimento.diasDesdeProcedimento < 30);
            const tratamentoDentario = this.procedimentos.some(procedimento => procedimento.selected === 'tratamento_dentario' && procedimento.diasDesdeProcedimento < 30);
            const remocaoTartaro = this.procedimentos.some(procedimento => procedimento.selected === 'remocao_tartaro' && procedimento.diasDesdeProcedimento < 3);
            const outrosProcedimentos = this.procedimentos.some(procedimento => procedimento.selected === 'outros_procedimentos' && procedimento.diasDesdeProcedimento < 3);
            const tatuagem12Meses = this.tatuagem === 'sim' && this.tatuagemRegistro === 'nao';
            const tatuagem6Meses = this.tatuagem === 'sim' && this.tatuagemRegistro === 'sim' && this.tatuagem6 === 'sim';
            const soroHumano = this.soro === 'sim' && this.soroTipo === 'humano';
            const soroNormal = this.soro === 'sim' && this.soroTipo === 'normal' && this.soro4Semanas === 'sim';
            const usoAnabolizantes = this.drogaAnabolizante === 'sim';
            const usoAyahuasca = this.drogaAyahuasca === 'sim';
            const usoDrogasGerais = this.drogaGerais === 'sim';
            const sexo3Parcerias6Meses = this.sexo === 'sim' && this.sexo3Parcerias === 'sim';
            const sexoCasual12Meses = this.sexo === 'sim' && this.sexoCasual === 'sim';
            const sexoSimultaneo12Meses = this.sexo === 'sim' && this.sexoSimultaneo === 'sim';
            const viajarMalaria30Dias = this.viagemMalaria === 'sim';
            const mulherGravida = this.genero === 'feminino' && this.gravidez === 'agora';
            const mulherPartoNormalRecente = this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'normal' && this.partoRecente === 'sim';
            const mulherCesarianaRecente = this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'cesariana' && this.cesarianaRecente === 'sim';
            const mulherAmamentando = this.genero === 'feminino' && this.gravidez === 'recente' && this.amamentacao === 'sim';
            const mulherAbortoRecente = this.genero === 'feminino' && this.gravidez === 'recente' && this.parto === 'aborto' && this.abortoRecente === 'sim';

            return menorDe16 || semAutorizacao || maiorQue60SemDoacao || pesoInadequado || imcSuperobeso || imcObesidadeSevera || ultimaDoacaoMulher || ultimaDoacaoHomem || sintomasDoenca || reacaoAlergica || ajusteAparelhoSemSangramento || ajusteAparelhoComSangramento || carieSemAnestesiaSemSangramento || carieSemAnestesiaComSangramento ||
                carieComAnestesia || drenagemAbscesso || tratamentoCanal || tratementoGengivites || outrasCirurgias || implanteDentario || implanteDentarioAssintomatico || tratamentoDentario || remocaoTartaro || outrosProcedimentos || vacinaPolioOral || vacinaFebreTifoideOral || vacinaCaxumba || vacinaFebreAmarela || vacinaSarampo ||
                vacinaBCG || vacinaRubeola || vacinaVaricela || vacinaVariola || vacinaDengue || vacinaColera || vacinaPolio || vacinaDifteria || vacinaTetano || vacinaFebreTifoideInjetavel || vacinaMeningite || vacinaCoqueluche || vacinaHepatiteA || vacinaPeste || vacinaPneumococo || vacinaLeptospirose || vacinaBrucelose ||
                vacinaHemophillus || vacinaHepatiteBRecombinante || vacinaCovidCoronavac || vacinaCovidOutras || vacinaHPV || vacinaInfluenza || vacinaSoroAntitetanico || vacinaAntirrabicaProfilatica || vacinaAntirrabicaAposAnimal || vacinaHepatiteBPlasma || tatuagem12Meses || tatuagem6Meses || soroHumano || soroNormal ||
                usoAnabolizantes || usoAyahuasca || usoDrogasGerais || sexo3Parcerias6Meses || sexoCasual12Meses || sexoSimultaneo12Meses || viajarMalaria30Dias || mulherGravida || mulherPartoNormalRecente || mulherCesarianaRecente || mulherAmamentando || mulherAbortoRecente;
        },

        inaptidaoDefinitiva() {
            const maiorQue70 = this.idade >= 70;
            const alcoolismoCronico = this.drogaBebida === 'sim' && this.drogaAlcolatra === 'sim';
            const usoDrogasInjetaveis = this.drogaInjetavel === 'sim';
            const mulherAbortoRepeticao = this.genero === 'feminino' && this.abortoRepeticao === 'sim';
            const morarReinoUnido3Meses = this.viagemReinoUnido === 'sim';
            const morarEuropa5anos = this.viagemEuropa === 'sim';

            return maiorQue70 || alcoolismoCronico || usoDrogasInjetaveis || mulherAbortoRepeticao || morarReinoUnido3Meses || morarEuropa5anos;
        }
    },
    methods: {
        // Avança para o próximo step
        nextStep() {
            this.errorMessage = '';
            if (this.step < 15) {
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
        // Cálculo dos dias desde a última doação
        daysFromLastDonation() {
            if (this.ultimaDoacao) {
                const dataUltimaDoacao = new Date(this.ultimaDoacao);
                const dataAtual = new Date();
                const diferencaEmMilissegundos = dataAtual - dataUltimaDoacao;
                const dias = Math.floor(diferencaEmMilissegundos / (1000 * 60 * 60 * 24));
                this.diasDesdeUltimaDoacao = dias;
            } else {
                this.diasDesdeUltimaDoacao = null;
            }
        },
        addVaccine() {
            this.vacinas.push({ selected: null, diaVacina: '', coronavac: null })
        },
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
        addProcedure() {
            this.procedimentos.push({ selected: null, diaOdontologico: '', sangramento: null, medicacaoDental: null, assintomaticosDental: null });
        },
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
    background-color: #F0F0F0;
    border-radius: 2.5px;
    margin-bottom: 40px;
}

.progress-bar {
    height: 100%;
    background-color: #E90B0B;
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
    background-color: #F0F0F0;
    border-radius: 50%;
    transition: background-color 0.3s ease-in-out;
}

.progress-step.active {
    background-color: #E90B0B;
}


/* Estilos dos textos */
h2 {
    color: #000000;
    font-size: 1.4em;
    margin: 10px 0 30px;
    font-weight: normal;
    text-align: center;
}

h4 {
    color: #000000;
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
    color: #555;
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
select {
    display: block;
    width: 100%;
    box-sizing: border-box;
    border: 1px solid #BBB;
    border-radius: 5px;
    color: #555;
    padding: 6px 10px;
    margin-bottom: 10px;
}

input[type="number"]:active select:active {
    border: 1px solid #BBB;
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
    border: 1px solid #DDD;
    background-color: #EEE;
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
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
    border: 1px solid #BBB;
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
    background-color: #E90B0B;
    color: #FFF;
    border-color: #E90B0B;
}


/* Estilo das mensagens de erro */
.error-message {
    color: red;
    font-size: 0.8em;
    margin-top: -15px;
    margin-bottom: 15px;
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
    background-color: #DDD;
    color: #111;
}

.button_next {
    background-color: #E90B0B;
    color: #FFF;
    margin-left: auto;
}

.button_finish {
    background-color: #0BB54C;
    color: #FFFFFF;
}

.button_add {
    background-color: #EAEAEA;
    border-radius: 3px;
    padding: 8px 5px;
}
</style>