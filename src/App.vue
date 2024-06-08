<template>
  <div class="contenu-calc">
    <h2>Calculatrice</h2><br>
    <div class="corps-calc">
      <div class="champs">
        <input type="text" v-model="inputValue" placeholder="0" @keyup.enter="calculer" @keyup.esc="reinitialiseTout" ref="input">
        <p class="reponse">{{ reponse }}</p>
      </div>
      <br>
      <div class="contenu-button">
        <div class="contenu-button-haut">
          <button class="supplementaires" v-for="supplement in supplementaires" :key="supplement" @click="afficheOperateur(supplement)">{{ supplement }}</button>
          
        </div>
        <div class="contenu-button-bas">
          <div class="partie-gauche">
            
            <button class="valeurs" v-for="valeur in valeurs" :key="valeur" @click="afficheValeur(valeur)">{{ valeur }}</button>
            <button @click="calculer" class="resultat">=</button>
            <button @click="inputValue += 'π'" class="pi">π</button>
            <button @click="effaceDernier" class="delete">DEL</button>
            <button @click="reinitialiseTout" class="esc">AC</button> 
          </div>
          <div class="partie-droite">
            <button class="operateurs" v-for="operateur in operateurs" :key="operateur" @click="afficheValeur(operateur)">{{ operateur }}</button>
          </div>

        </div>
      </div>
      
      <br>
    </div>
  </div>
  
</template>

<script>
export default {
  data(){
    return{
      inputValue : '',
      inputTmp : '',
      reponse : '',
      valeurs : [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, '.'],
      operateurs : ['+', '-', '/', '*', '%'],
      supplementaires : ['√', '^', 'ln', 'e', '!', '(', ')', 'sin', 'cos', 'tan'],
    }
  },
  methods: {
    factorielle(n){
      let produit=1;
      for(let i = 1 ; i<=n; i++){
        produit = produit*i
      }
      return produit
    },
    afficheValeur(valeur){
      if (typeof valeur === 'number') {
        this.inputValue += valeur.toString();
      } else {
        this.inputValue += valeur;
      }
      this.$refs.input.focus();
    },
    afficheOperateur(operateur){
      if (operateur === '(' || operateur === ')' || operateur === '!') {
        this.afficheValeur(operateur);
      } else {
        this.inputValue += operateur + '(';
        this.$refs.input.focus();
      }
    },
    calculer() {
      try{
        this.inputTmp = this.inputValue; 

        //avec parenthèses
        let regex = /\(([^\(\)]+)\)!/g;
        while (regex.test(this.inputTmp)) {
          this.inputTmp = this.inputTmp.replace(regex, (avecExclam, expression) => {
            return this.factorielle(eval(expression));
          });
        }

        //sans parenthèses
        this.inputTmp = this.inputTmp.replace(/(\d+)!/g, (avecExclam, nombre) => {
          return this.factorielle(parseInt(nombre));
        });

        for (let i = 0; i < this.inputValue.length; i++) {
          switch (this.inputValue[i]) {
            case '^':
              this.inputTmp = this.inputTmp.replace('^', "**");
              break;
            case '√':    
              this.inputTmp = this.inputTmp.replace('√', "Math.sqrt");     
              break;
            case 'e':
              this.inputTmp = this.inputTmp.replace('e', "Math.exp");
              break;
            case 'l':
              this.inputTmp = this.inputTmp.replace('ln', "Math.log");
              break;
            case 'π':
              this.inputTmp = this.inputTmp.replace('π', "Math.PI");
              break;
            case 'c':
              this.inputTmp = this.inputTmp.replace('cos', "Math.cos");
              break;
            case 's':
              this.inputTmp = this.inputTmp.replace('sin', "Math.sin");
              break;
            case 't':
              this.inputTmp = this.inputTmp.replace('tan', "Math.tan");
              break;
          }
        }
        console.log(this.inputTmp);
        this.reponse = eval(this.inputTmp);
      }catch(e){
        this.reponse = "Syntaxe Error";
      }
      this.$refs.input.focus();
    },
    effaceDernier(){
      if (this.reponse) {
        return;
      } else {
        const operateurAvecParenthese = this.inputValue.slice(-2);
        if (this.supplementaires.includes(operateurAvecParenthese[0]) && operateurAvecParenthese[1] === '(') {
          this.inputValue = this.inputValue.slice(0, -2);
        } else {
          this.inputValue = this.inputValue.slice(0, -1);
        }

        this.$refs.input.focus();
      }
    },
    reinitialiseTout(){
      this.inputValue = ''
      this.reponse = ''
      this.$refs.input.focus();
    }
  }
}
</script>

<style>

</style>