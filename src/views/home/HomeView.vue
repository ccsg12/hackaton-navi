<template>
  <v-container fluid>
    <section id="introduction">
      <banner></banner>
    </section>

    <section id="context" class="wrapper">
      <div class="inner">
        <header class="special2">
          <h1>Contexto</h1>
          <br>
        </header>

        <div class="count2">
          <p class="description">
            Hoje, o setor de energia elétrica brasileiro tem influência de três grandes áreas. Na área ambiental,
            existe uma tendência para o investimento em fontes sustentáveis, de modo a diminuir o impacto ambiental
            e continuar suprindo a necessidade energética do país.
          </p>

          <p class="description">
            Na área financeira, existe uma crescente migração das indústrias e empresas grandes para contratos de
            mercado livre, nos quais essas conseguem estabelecer acordos mais benéficos diretamente com as empresas
            geradoras de energia.
          </p>

          <p class="description">
            Para a segurança, existe uma preocupação constante em garantir estabilidade de rede mesmo em épocas de
            crise. Assim, existe um esforço para diversificar a matriz energética brasileira, tornando-a menos
            dependente dos recursos hídricos.
          </p>
        </div>
      </div>
    </section>

    <section id="cta" class="wrapper">
      <h1>Desafio</h1>

      <div class="inner">
        <div class="content">
          <p class="description">
            Apesar do mercado de <strong>Geração Distribuída</strong> ter sido aprovado economico e sustentavelmente,
            trazendo segurança para as distribuidoras no mercado regulado, seu consumo é baixo devido a estigmas que
            poderiam, estrategicamente, ser retirados, aumentando esse consumo. A baixa popularização da GD ofusca o seu
            real potencial. Uma vez que as pessoas não a conhecem e negligenciam o mercado livre de energia não há uma
            oportunidade mínima de alcançar novos consumidores. Devido a isso, o <strong>Clagedis</strong> visa aumentar
            a taxa de conversão e diminuir o custo de aquisição dos clientes, diminuindo o ciclo de venda.
          </p>
        </div>
      </div>
    </section>

    <section id="map" class="wrapper">
      <header class="special2">
        <h1>Mapa de classificação</h1>
        <br>
      </header>

      <div class="inner">
        <p class="description">
          Mapa de calor do território brasileiro, alimentado por um modelo que leva em consideração diversos parâmetros, tais como:
          Irradiação solar, Número de casas, População, PIB per capita, Penetração da GD, ICMS, Custo de mão de obra, Índice de Burocracia e Taxa de inadimplência.
        </p>
        <div class="map-container">
          <l-map :zoom="zoom" :center="center">
            <l-tile-layer :url="url"/>

            <template v-for="(geoJson, index) in geoJsons">
              <l-geo-json
                  :geojson="geoJson"
                  :options="options"
                  :options-style="() => geoJsonLayerStyle(geoJson.color)"
                  :key="index"
              />
            </template>
          </l-map>
        </div>
      </div>
    </section>

    <footer id="footer">
      <div class="inner">
        <div class="content">
          <section>
            <h3>Nosso time</h3>
            <p>
              Cytel é um grupo de estudantes de engenharia que identificou o problema de conversão de clientes das
              empresas de GD no Brasil e propôs esse modelo que os auxilia nesse quesito.
            </p>
          </section>

          <section>
            <h3>Links do projeto</h3>

            <ul class="alt">
              <li>
                <a href="https://www.canva.com/design/DAD9wvKaeIM/ino76mIDTnbYIBzKSEoeKw/view?utm_content=DAD9wvKaeIM&utm_campaign=designshare&utm_medium=link&utm_source=sharebutton">
                  Vídeo - Pitch do projeto
                </a>
              </li>

              <li>
                <a href="https://github.com/ccsg12/hackaton-navi">
                  Github - Página web
                </a>
              </li>
            </ul>
          </section>
        </div>

        <div class="copyright">
          &copy; Clagedis - Developed by "CYTEL".
        </div>
      </div>
    </footer>
  </v-container>
</template>

<script>
import {latLng} from "leaflet";
import {LMap, LTileLayer, LGeoJson} from "vue2-leaflet";
import 'leaflet/dist/leaflet.css';
import "./styles.scss"
import _ from "lodash"

import dataJson from '@/assets/geojson_gd_score.json'

import Banner from "@/components/banner";

export default {
  name: 'HomeView',

  components: {
    Banner,
    LMap,
    LTileLayer,
    LGeoJson
  },

  data() {
    return {
      enableTooltip: true,
      zoom: 4,
      center: latLng(-15.474279, -58.084098),
      geoJsons: [],
      colors: [
        "#f2efe9",
        "#f3e5df",
        "#f3dbd5",
        "#f4d1cb",
        "#f5c6c1",
        "#f5b1ac",
        "#f6a7a3",
        "#f69c99",
        "#f88885",
        "#f87e7b",
        "#f97371",
        "#f96967",
        "#fa5f5d",
        "#fb5552",
        "#fb4a48",
        "#fc403e",
        "#fc3533",
        "#fc2a29",
        "#fd201f",
        "#fe0b0b",
      ],
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'
    };
  },

  computed: {
    options() {
      return {
        onEachFeature: this.onEachFeatureFunction
      };
    },

    onEachFeatureFunction() {
      if (!this.enableTooltip) {
        return () => {
        };
      }

      return (feature, layer) => {
        layer.bindTooltip(
            `<div>name: ${feature.properties.NM_MUN} - ${feature.properties.SIGLA}</div>
            <div>score: ${feature.properties.tabela_consolidada_GD_SCORE.toFixed(2)}</div>`,
            {permanent: false, sticky: true}
        );
      };
    }
  },

  methods: {
    geoJsonLayerStyle(color) {
      return {
        weight: 2,
        color: "#a45a4500",
        opacity: 1,
        fillColor: color,
        fillOpacity: 1
      }
    },
  },

  mounted() {
    setTimeout(() => document.getElementById('banner').classList.remove('is-preload'), 500)
  },

  async created() {
    const intervalIndexes = [...Array(20).keys()]

    for (const intervalIndex of intervalIndexes) {
      let json = _.omit(dataJson, ["features"])
      const min = intervalIndex / 20
      const max = (intervalIndex + 1) / 20

      json.features = dataJson.features.filter(feature => {
        return feature.properties.tabela_consolidada_GD_SCORE > min && feature.properties.tabela_consolidada_GD_SCORE <= max
      })
      json.color = this.colors[intervalIndex]

      this.geoJsons.push(json)
    }
  }
}
</script>
