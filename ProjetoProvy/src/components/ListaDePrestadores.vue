<template>
    <div class="main">
        <div class="mainHeader">
            <div class="title">
                <h1 @click="clearFilters">Provy</h1>
            </div>
            <div class="search">
                <input class="Pesquisar" type="text" v-model="searchQuery" placeholder="Buscar prestador...">
                <button class="search-button" @click="searchPrestadores">Pesquisar</button>
            </div>
        </div>
        <div class="mainBody">
            <h2 class="title-centralizada">Encontre o prestador(a) ideal</h2>
            <div class="filter-buttons">
                <div class="profissoes-container">
                    <button :class="{'active': activeFilter.profissao}" @click="toggleProfissoes">{{ activeFilter.profissao || 'Profissões' }}</button>
                    <div v-if="showProfissoes" class="profissoes-dropdown">
                        <button @click="selectProfissao('')">Todos</button>
                        <button @click="selectProfissao('Eletricista')">Eletricista</button>
                        <button @click="selectProfissao('Faxineira')">Faxineira</button>
                        <button @click="selectProfissao('Encanador')">Encanador</button>
                    </div>
                </div>
                <div class="sexo-container">
                    <button :class="{'active': activeFilter.sexo}" @click="toggleSexo">{{ activeFilter.sexo === 'F' ? 'Feminino' : activeFilter.sexo === 'M' ? 'Masculino' : 'Sexo' }}</button>
                    <div v-if="showSexo" class="sexo-dropdown">
                        <button @click="selectSexo('')">Todos</button>
                        <button @click="selectSexo('F')">Feminino</button>
                        <button @click="selectSexo('M')">Masculino</button>
                    </div>
                </div>
                <div class="ordenar-container">
                    <button :class="{'active': activeSort}" @click="toggleOrdenar">{{ activeSort === 'avaliacao' ? 'Avaliação' : activeSort === 'preco' ? 'Preço' : 'Ordenar' }}</button>
                    <div v-if="showOrdenar" class="ordenar-dropdown">
                        <button @click="selectOrdenar('avaliacao')">Avaliação</button>
                        <button @click="selectOrdenar('preco')">Preço</button>
                    </div>
                </div>
            </div>
            <div class="Prestadores">
                <div v-for="prestador in filteredPrestadores" :key="prestador.nome" class="card">
                    <h3>{{ prestador.nome }}</h3>
                    <p><strong>Idade:</strong> {{ prestador.idade }} anos</p>
                    <p><strong>Profissão:</strong> {{ prestador.profissao }}</p>
                    <p><strong>Sexo:</strong> {{ prestador.sexo }}</p>
                    <p><strong>Experiência:</strong> {{ prestador.experiencia }} anos</p>
                    <p><strong>Avaliação:</strong> ★{{ prestador.avaliacao }} ({{ prestador.avaliacao }}/5)</p>
                    <p><strong>Preço:</strong> {{ prestador.preco }}</p>
                    <p><strong>Serviços:</strong> {{ prestador.servicos.join(', ') }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            searchQuery: '',
            showProfissoes: false,
            showSexo: false,
            showOrdenar: false,
            activeFilter: {
                profissao: '',
                sexo: ''
            },
            activeSort: '',
            prestadores: [
                { nome: 'João Silva', idade: 35, profissao: 'Encanador', sexo: 'M', experiencia: 10, avaliacao: 4, preco: '$$', servicos: ['Conserto de vazamentos', 'Instalação de torneiras'] },
                { nome: 'Maria Oliveira', idade: 28, profissao: 'Encanadora', sexo: 'F', experiencia: 5, avaliacao: 5, preco: '$$', servicos: ['Desentupimento', 'Instalação de sistemas de água'] },
                { nome: 'Carlos Pereira', idade: 40, profissao: 'Eletricista', sexo: 'M', experiencia: 15, avaliacao: 3, preco: '$$$', servicos: ['Instalação elétrica', 'Reparos em fiação'] },
                { nome: 'Ana Costa', idade: 32, profissao: 'Faxineira', sexo: 'F', experiencia: 8, avaliacao: 4, preco: '$', servicos: ['Limpeza geral', 'Organização de ambientes'] },
                { nome: 'Pedro Souza', idade: 30, profissao: 'Jardineiro', sexo: 'M', experiencia: 7, avaliacao: 5, preco: '$$', servicos: ['Corte de grama', 'Poda de árvores'] },
                { nome: 'Luciana Almeida', idade: 45, profissao: 'Pintora', sexo: 'F', experiencia: 12, avaliacao: 4, preco: '$$$', servicos: ['Pintura de paredes', 'Acabamentos'] }
            ],
            filteredPrestadores: []
        };
    },
    methods: {
        searchPrestadores() {
            this.filteredPrestadores = this.prestadores.filter(prestador =>
                prestador.profissao.toLowerCase().includes(this.searchQuery.toLowerCase())
            );
        },
        toggleProfissoes() {
            this.showProfissoes = !this.showProfissoes;
            this.showSexo = false;
            this.showOrdenar = false;
        },
        toggleSexo() {
            this.showSexo = !this.showSexo;
            this.showProfissoes = false;
            this.showOrdenar = false;
        },
        toggleOrdenar() {
            this.showOrdenar = !this.showOrdenar;
            this.showProfissoes = false;
            this.showSexo = false;
        },
        selectProfissao(profissao) {
            this.activeFilter.profissao = profissao;
            this.filteredPrestadores = this.prestadores.filter(prestador =>
                !profissao || 
                prestador.profissao.toLowerCase() === profissao.toLowerCase() || 
                (profissao === '' && prestador.profissao)
            );
            this.showProfissoes = false;
        },
        selectSexo(sexo) {
            this.activeFilter.sexo = sexo;
            this.filteredPrestadores = this.prestadores.filter(prestador =>
                !sexo || prestador.sexo === sexo
            );
            this.showSexo = false;
        },
        selectOrdenar(criteria) {
            this.activeSort = criteria;
            if (criteria === 'avaliacao') {
                this.filteredPrestadores.sort((a, b) => b.avaliacao - a.avaliacao);
            } else if (criteria === 'preco') {
                const priceOrder = { '$': 1, '$$': 2, '$$$': 3, '$$$$': 4 };
                this.filteredPrestadores.sort((a, b) => priceOrder[a.preco] - priceOrder[b.preco]);
            }
            this.showOrdenar = false;
        },
        clearFilters() {
            this.searchQuery = '';
            this.filteredPrestadores = this.prestadores;
            this.activeFilter = { profissao: '', sexo: '' };
            this.activeSort = '';
            this.showProfissoes = false;
            this.showSexo = false;
            this.showOrdenar = false;
        }
    },
    created() {
        this.filteredPrestadores = this.prestadores;
    }
};
</script>

<style>

body {
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
    color: white;
    background-color: #f8f8f8;
    margin: 0;
    padding: 0;
}

.mainHeader {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px 30px;
    background-color: #4040ff;
    color: white;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    border-radius: 30px;
}

.title h1 {
    margin: 0;
    cursor: pointer;
    font-size: 1.8rem;
    font-weight: 700;
    letter-spacing: 1px;
    color: white;
}


.search {
    color: blue;
    display: flex;
    align-items: stretch; 
}

.Pesquisar {
    padding: 10px;
    border: 2px solid #ecf0f1;
    border-radius: 20px 0 0 20px;
    font-size: 1rem;
    outline: none;
    flex: 1; 
    transition: border-color 0.3s;
}

.search-button {
    padding: 10px 20px;
    border: none;
    border-radius: 0 20px 20px 0;
    background-color: #ecf0f1;
    color: #3498db;
    font-size: 1rem;
    font-weight: bold;
    cursor: pointer;
    transition: background-color 0.3s, color 0.3s;
    white-space: nowrap; /* Impede que o texto dentro do botão quebre em várias linhas */
}

.search-button:hover {
    background-color: #2980b9;
    color: white;
}

.mainBody {
    padding: 40px 20px;
    max-width: 1200px;
    margin: 0 auto;
}

.title-centralizada {
    text-align: center;
    font-size: 1.5rem;
    color:  white;
    margin-bottom: 10px;
}

.filter-buttons {
    display: flex;
    justify-content: flex-end;
    margin-bottom: 20px;
    gap: 20px;
}

.filter-container {
    position: relative;
    margin-left: 10px;
}

.filter-container button {
    padding: 12px 25px;
    border: 2px solid #bdc3c7;
    border-radius: 20px;
    background-color: transparent;
    color: #7f8c8d;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.3s, color 0.3s;
}

.filter-container button.active {
    background-color: #3498db;
    color: white;
}

.dropdown {
    position: absolute;
    top: 50px;
    left: 0;
    background: white;
    border: 1px solid #3498db;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    z-index: 100;
    width: 100%;
}

.dropdown button {
    display: block;
    padding: 10px 15px;
    width: 100%;
    text-align: left;
    border: none;
    background: none;
    color: #3498db;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.3s;
}

.dropdown button:hover {
    background-color: #ecf0f1;
}

.Prestadores {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
}

.card {
    background-color: white;
    border: 1px solid #ecf0f1;
    border-radius: 12px;
    padding: 20px;
    width: 220px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    text-align: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
}

.card h3 {
    font-size: 1.25rem;
    color: #2c3e50;
    margin-bottom: 10px;
}

.card p {
    font-size: 0.9rem;
    color: #7f8c8d;
    margin: 5px 0;
}

@media (max-width: 768px) {
    .mainHeader {
        flex-direction: column;
        text-align: center;
    }

    .filter-buttons {
        flex-direction: column;
        align-items: center;
        gap: 10px;
    }

    .search {
        margin-top: 20px;
    }

    .card {
        width: 100%;
        max-width: 300px;
    }
}


</style>
