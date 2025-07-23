Statui relocate


    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="UTF-8"/>
        <title>Kepler.gl embedded map</title>

        <!--Uber Font-->
        <link rel="stylesheet" href="https://d1a3f4spazzrp4.cloudfront.net/kepler.gl/uber-fonts/4.0.0/superfine.css">

        <!--MapBox css-->
        <link href="https:https://unpkg.com/maplibre-gl@^3/dist/maplibre-gl.css" rel="stylesheet">

        <!-— facebook open graph tags -->
        <meta property="og:url" content="http://kepler.gl/" />
        <meta property="og:title" content="Large-scale WebGL-powered Geospatial Data Visualization Tool" />
        <meta property="og:description" content="Kepler.gl is a powerful web-based geospatial data analysis tool. Built on a high performance rendering engine and designed for large-scale data sets." />
        <meta property="og:site_name" content="kepler.gl" />
        <meta property="og:image" content="https://d1a3f4spazzrp4.cloudfront.net/kepler.gl/kepler.gl-meta-tag.png" />
        <meta property="og:image:type" content="image/png" />
        <meta property="og:image:width" content="800" />
        <meta property="og:image:height" content="800" />

        <!-— twitter card tags -->
        <meta name="twitter:card" content="summary_large_image">
        <meta name="twitter:site" content="@openjsf">
        <meta name="twitter:creator" content="@openjsf">
        <meta name="twitter:title" content="Large-scale WebGL-powered Geospatial Data Visualization Tool">
        <meta name="twitter:description" content="Kepler.gl is a powerful web-based geospatial data analysis tool. Built on a high performance rendering engine and designed for large-scale data sets.">
        <meta name="twitter:image" content="https://d1a3f4spazzrp4.cloudfront.net/kepler.gl/kepler.gl-meta-tag.png" />

        <!-- Load React/Redux -->
        <script src="https://unpkg.com/react@18.2.0/umd/react.production.min.js" crossorigin></script>
        <script src="https://unpkg.com/react-dom@18.2.0/umd/react-dom.production.min.js" crossorigin></script>
        <script src="https://unpkg.com/redux@4.2.1/dist/redux.js" crossorigin></script>
        <script src="https://unpkg.com/react-redux@8.1.2/dist/react-redux.min.js" crossorigin></script>
        <script src="https://unpkg.com/styled-components@4.4.1/dist/styled-components.min.js" crossorigin></script>

        <!-- Load Kepler.gl -->
        <script src="https://unpkg.com/kepler.gl@3.0.0/umd/keplergl.min.js" crossorigin></script>

        <style type="text/css">
          body {margin: 0; padding: 0; overflow: hidden;}
        </style>

        <!--MapBox token-->
        <script>
          /**
           * Provide your MapBox Token
           **/
          const MAPBOX_TOKEN = 'pk.eyJ1Ijoidm9pY2EzNyIsImEiOiJjbTJpMnVkMTMwaGxmMmlxdm91a2lmejRoIn0.At890vg6ss_RgBljaaE_Bg';
          const WARNING_MESSAGE = 'Please Provide a Mapbox Token in order to use Kepler.gl. Edit this file and fill out MAPBOX_TOKEN with your access key';
        </script>

        <!-- GA: Delete this as you wish, However to pat ourselves on the back, we only track anonymous pageview to understand how many people are using kepler.gl. -->
        <script>
          (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
          (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
          m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
          })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
          ga('create', 'UA-64694404-19', {
            'storage': 'none',
            'clientId': localStorage.getItem('ga:clientId')
          });
          ga(function(tracker) {
              localStorage.setItem('ga:clientId', tracker.get('clientId'));
          });
          ga('set', 'checkProtocolTask', null); // Disable file protocol checking.
          ga('set', 'checkStorageTask', null); // Disable cookie storage checking.
          ga('set', 'historyImportTask', null); // Disable history checking (requires reading from cookies).
          ga('set', 'page', 'keplergl-html');
          ga('send', 'pageview');
        </script>
      </head>
      <body>
        <!-- We will put our React component inside this div. -->
        <div id="app">
          <!-- Kepler.gl map will be placed here-->
        </div>

        <!-- Load our React component. -->
        <script>
          /* Validate Mapbox Token */
          if ((MAPBOX_TOKEN || '') === '' || MAPBOX_TOKEN === 'PROVIDE_MAPBOX_TOKEN') {
            alert(WARNING_MESSAGE);
          }

          /** STORE **/
          const reducers = (function createReducers(redux, keplerGl) {
            return redux.combineReducers({
              // mount keplerGl reducer
              keplerGl: keplerGl.keplerGlReducer.initialState({
                uiState: {
                  readOnly: false,
                  currentModal: null
                }
              })
            });
          }(Redux, KeplerGl));

          const middleWares = (function createMiddlewares(keplerGl) {
            return keplerGl.enhanceReduxMiddleware([
              // Add other middlewares here
            ]);
          }(KeplerGl));

          const enhancers = (function craeteEnhancers(redux, middles) {
            return redux.applyMiddleware(...middles);
          }(Redux, middleWares));

          const store = (function createStore(redux, enhancers) {
            const initialState = {};

            return redux.createStore(
              reducers,
              initialState,
              redux.compose(enhancers)
            );
          }(Redux, enhancers));
          /** END STORE **/

          /** COMPONENTS **/
          var KeplerElement = (function makeKeplerElement(react, keplerGl, mapboxToken) {
            var LogoSvg = function LogoSvg() {
              return react.createElement(
                "div",
                { className: "logo-container", style: {position: 'fixed', zIndex: 10000, padding: '4px'} },
                  react.createElement(
                    "svg",
                    {
                      className: "kepler_gl__logo",
                      width: "107px",
                      height: "21px",
                      viewBox: "0 0 124 24"
                    },
                    react.createElement(
                      "g",
                      { transform: "translate(13.500000, 13.500000) rotate(45.000000) translate(-13.500000, -13.500000) translate(4.000000, 4.000000)" },
                      react.createElement("rect", { x: "0", y: "6", transform: "matrix(2.535181e-06 1 -1 2.535181e-06 18.1107 6.0369)", fill: "#535C6C", width: "12.1", height: "12.1" }),
                      react.createElement("rect", { x: "6", y: "0", transform: "matrix(2.535182e-06 1 -1 2.535182e-06 18.1107 -6.0369)", fill:"#1FBAD6", width: "12.1", height: "12.1" })
                    ),
                    react.createElement(
                      "g",
                      {},
                      react.createElement("path", { fill:"#1FBAD6", d: "M39,8.7h2.2l-2.8,4.2l2.9,5.1H39l-2.4-4.2h-1.3V18h-2V5l2-0.1v7.3h1.3L39,8.7z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M42.4,13.3c0-1.5,0.4-2.7,1.1-3.5s1.8-1.2,3.1-1.2c1.3,0,2.2,0.4,2.8,1.1c0.6,0.7,0.9,1.8,0.9,3.3 c0,0.4,0,0.8,0,1.1h-5.8c0,1.6,0.8,2.4,2.4,2.4c1,0,2-0.2,2.9-0.6l0.2,1.7c-0.4,0.2-0.9,0.4-1.4,0.5s-1.1,0.2-1.7,0.2 c-1.5,0-2.6-0.4-3.3-1.2C42.8,16.1,42.4,14.9,42.4,13.3z M46.6,10.1c-0.7,0-1.2,0.2-1.5,0.5c-0.4,0.4-0.6,0.9-0.6,1.7h4 c0-0.8-0.2-1.4-0.5-1.7S47.2,10.1,46.6,10.1z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M57.1,18.2c-1,0-1.8-0.3-2.3-0.9l0,0l0,1.3v2.5h-2V8.7h1.5l0.3,0.9h0c0.3-0.3,0.7-0.6,1.2-0.7 c0.4-0.2,0.9-0.3,1.4-0.3c1.2,0,2.1,0.4,2.7,1.1c0.6,0.7,0.9,2,0.9,3.7c0,1.6-0.3,2.8-1,3.7C59.2,17.8,58.3,18.2,57.1,18.2z M56.7,10.3c-0.4,0-0.8,0.1-1.1,0.2c-0.3,0.2-0.6,0.4-0.8,0.7v4.3c0.2,0.3,0.4,0.5,0.7,0.7c0.3,0.2,0.7,0.3,1.1,0.3 c0.7,0,1.2-0.2,1.6-0.7c0.4-0.5,0.5-1.3,0.5-2.5c0-0.8-0.1-1.4-0.2-1.8s-0.4-0.7-0.7-0.9C57.6,10.4,57.2,10.3,56.7,10.3z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M63.2,16V5l2-0.1v10.8c0,0.3,0.1,0.5,0.2,0.6c0.1,0.1,0.3,0.2,0.6,0.2c0.3,0,0.6,0,0.9-0.1V18 c-0.4,0.1-1,0.2-1.6,0.2c-0.8,0-1.3-0.2-1.7-0.5S63.2,16.8,63.2,16z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M68.2,13.3c0-1.5,0.4-2.7,1.1-3.5c0.7-0.8,1.8-1.2,3.1-1.2c1.3,0,2.2,0.4,2.8,1.1c0.6,0.7,0.9,1.8,0.9,3.3 c0,0.4,0,0.8,0,1.1h-5.8c0,1.6,0.8,2.4,2.4,2.4c1,0,2-0.2,2.9-0.6l0.2,1.7c-0.4,0.2-0.9,0.4-1.4,0.5s-1.1,0.2-1.7,0.2 c-1.5,0-2.6-0.4-3.3-1.2C68.6,16.1,68.2,14.9,68.2,13.3z M72.4,10.1c-0.7,0-1.2,0.2-1.5,0.5c-0.4,0.4-0.6,0.9-0.6,1.7h4 c0-0.8-0.2-1.4-0.5-1.7S73,10.1,72.4,10.1z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M80.2,8.7l0.1,1.7h0c0.3-0.6,0.7-1.1,1.1-1.4c0.4-0.3,1-0.5,1.6-0.5c0.4,0,0.7,0,1,0.1l-0.1,2 c-0.3-0.1-0.7-0.2-1-0.2c-0.7,0-1.3,0.3-1.7,0.8c-0.4,0.5-0.7,1.2-0.7,2.1V18h-2V8.7H80.2z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M83.8,17c0-0.8,0.4-1.2,1.2-1.2c0.8,0,1.2,0.4,1.2,1.2c0,0.8-0.4,1.1-1.2,1.1C84.2,18.2,83.8,17.8,83.8,17z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M88.5,18.7c0-0.8,0.4-1.4,1.2-1.8c-0.6-0.3-0.9-0.8-0.9-1.5c0-0.7,0.4-1.2,1.1-1.6c-0.3-0.3-0.6-0.6-0.7-0.9 c-0.2-0.4-0.2-0.8-0.2-1.3c0-1,0.3-1.8,0.9-2.3c0.6-0.5,1.6-0.8,2.8-0.8c0.5,0,1,0,1.4,0.1c0.4,0.1,0.8,0.2,1.1,0.4l2.4-0.2v1.5 h-1.5c0.2,0.4,0.2,0.8,0.2,1.3c0,1-0.3,1.7-0.9,2.2s-1.5,0.8-2.7,0.8c-0.7,0-1.2-0.1-1.6-0.2c-0.1,0.1-0.2,0.2-0.3,0.3 c-0.1,0.1-0.1,0.2-0.1,0.4c0,0.2,0.1,0.3,0.2,0.4c0.1,0.1,0.3,0.2,0.6,0.2l2.7,0.2c1,0.1,1.7,0.3,2.2,0.6c0.5,0.3,0.8,0.9,0.8,1.7 c0,0.6-0.2,1.1-0.5,1.5c-0.4,0.4-0.9,0.8-1.5,1c-0.7,0.2-1.5,0.4-2.4,0.4c-1.3,0-2.3-0.2-3-0.6C88.8,20.1,88.5,19.5,88.5,18.7z M95.1,18.4c0-0.3-0.1-0.5-0.3-0.7s-0.6-0.2-1.1-0.3l-2.7-0.3c-0.2,0.1-0.4,0.3-0.5,0.5c-0.1,0.2-0.2,0.4-0.2,0.6 c0,0.4,0.2,0.8,0.5,1c0.4,0.2,1,0.3,1.8,0.3C94.2,19.5,95.1,19.2,95.1,18.4z M94.3,11.5c0-0.6-0.1-1-0.4-1.2 c-0.3-0.2-0.7-0.3-1.3-0.3c-0.7,0-1.1,0.1-1.4,0.3c-0.3,0.2-0.4,0.6-0.4,1.2s0.1,1,0.4,1.2c0.3,0.2,0.7,0.3,1.4,0.3 c0.6,0,1.1-0.1,1.3-0.4S94.3,12,94.3,11.5z" }),
                      react.createElement("path", { fill:"#1FBAD6", d: "M99.4,16V5l2-0.1v10.8c0,0.3,0.1,0.5,0.2,0.6c0.1,0.1,0.3,0.2,0.6,0.2c0.3,0,0.6,0,0.9-0.1V18 c-0.4,0.1-1,0.2-1.6,0.2c-0.8,0-1.3-0.2-1.7-0.5S99.4,16.8,99.4,16z" })
                    )
                  )
                );
              };

            return function App() {
              var rootElm = react.useRef(null);
              var _useState = react.useState({
                width: window.innerWidth,
                height: window.innerHeight
              });
              var windowDimension = _useState[0];
              var setDimension = _useState[1];
              react.useEffect(function sideEffect(){
                function handleResize() {
                  setDimension({width: window.innerWidth, height: window.innerHeight});
                };
                window.addEventListener('resize', handleResize);
                return function() {window.removeEventListener('resize', handleResize);};
              }, []);
              return react.createElement(
                'div',
                {style: {position: 'absolute', left: 0, width: '100vw', height: '100vh'}},
                
                react.createElement(keplerGl.KeplerGl, {
                  mapboxApiAccessToken: mapboxToken,
                  id: "map",
                  width: windowDimension.width,
                  height: windowDimension.height
                })
              )
            }
          }(React, KeplerGl, MAPBOX_TOKEN));

          const app = (function createReactReduxProvider(react, reactRedux, KeplerElement) {
            return react.createElement(
              reactRedux.Provider,
              {store},
              react.createElement(KeplerElement, null)
            )
          }(React, ReactRedux, KeplerElement));
          /** END COMPONENTS **/

          /** Render **/
          (function render(react, reactDOM, app) {
            reactDOM.render(app, document.getElementById('app'));
          }(React, ReactDOM, app));
        </script>
        <!-- The next script will show how to interact directly with Kepler map store -->
        <script>
          /**
           * Customize map.
           * In the following section you can use the store object to dispatch Kepler.gl actions
           * to add new data and customize behavior
           */
          (function customize(keplerGl, store) {
            const datasets = [{"version":"v1","data":{"id":"-5hx2pt","label":"Moved & Removed Statues Cluj-Napoca.csv","color":[42,206,241],"allData":[["Lupa Capitolina",10,"Da","P-ța Unirii","B-dul Eroilor","Copie după Antonio del Pollaiuolo (?) cu basorelief de Ettore Ferrari",1921,46.76924811,23.59044004,"regim","UBB",1967,46.76758311,23.59189038,"urbanism","Piața Unirii x Eroilor",1973,46.76926936,23.59108561,"urbanism","Eroilor",1994,46.76970702,23.59231663],["Căpeteniile maghiare din perioada descălecatului",6,"Nu","Aleea Parcului Central","Aleea Cetățuii (dispărut)","necunoscut",1885,46.77145611,23.58198584,"urbanism","În spatele bazinului de înot",1894,46.76654779,23.57314544,"urbanism","Aleea spre Cetățuie",1902,46.77380116,23.58321173,"urbanism",null,1918,46.77380116,23.58321173],["Széchenyi István",5,"Nu","P-ța Mihai Viteazu","(dispărut)","Klősz József",1867,46.77407267,23.5887843,"regim","Casa Matei",1933,46.77117305,23.58755494,"regim","Piața Mihai Viteazu",1943,46.77421555,23.58919397,null,null,null,null,null],["Lucian Blaga",15,"Da","În fața Casei de Cultură a Studenților","În fața Teatrului Național","Romul Ladea",1970,46.76709504,23.58667893,"regim","Parcul Central",1970,46.77044072,23.57921749,"regim","În fața Teatrului Național",1986,46.77067356,23.59726992,null,null,null,null,null],["Coloana ciumei",2,"Da","În fața seminarului iezuit","În fața seminarului iezuit","Anton Schuchbauer",1744,46.76753426,23.59039772,"regim","În spatele Bis. Sf. Petru & Pavel",1961,46.77685904,23.60769386,"urbanism","În fața Bis. Piariste",2023,46.76742197,23.590417,null,null,null,null,null],["Portalul baroc al Bis. Sf. Mihail",3,"Da","În fața Bis. Sf. Mihail","În fața Bis. Sf. Petru & Pavel","Johannes Nachtigall",1747,46.76989104,23.58902026,"urbanism","În fața Bis. Sf. Petru & Pavel",1899,46.77665617,23.60708297,null,null,null,null,null,null,null,null,null,null],["Obeliscul Carolina",4,"Da","P-ța Unirii","P-ța Muzeului","Sculptori: Antal Csűrös & Josef Klieber; Pietrari: Mihály & Antal Schindler",1831,46.76909278,23.59021337,"urbanism","Piața Muzeului",1898,46.77181413,23.58713406,null,null,null,null,null,null,null,null,null,null],["Sf. Gheorghe ucigând balaurul",8,"Da","În fața Casei de Cultură a Studenților","În fața Bis. calvine","József Róna",1904,46.76723356,23.58597592,"urbanism","Str. Kogălniceanu",1960,46.76809052,23.59523052,null,null,null,null,null,null,null,null,null,null],["Împărăteasa Sisi",7,"Nu în public","Dealul Cetățuie","Muzeul de artă Cluj","Alajos Stróbl",1900,46.77319969,23.58115042,"regim","Muzeul de Artă",1955,46.77084973,23.59059788,null,null,null,null,null,null,null,null,null,null],["Sf. Donath",1,"Nu în public","Dealul Hoia","Muzeul etnografic","necunoscut",1740,46.77462047,23.56065039,"urbanism","Parcul Muzeului Etnografic",2014,46.77836026,23.55619757,null,null,null,null,null,null,null,null,null,null],["Gheorghe Bilașcu",11,"Nu în public","Lângă Casa de Cultură a Studenților","Clinica de stomatologie","Frederic Storck",1929,46.76689436,23.58570363,"urbanism","Str. Clinicilor Nr. 32",1940,46.76534388,23.58007187,null,null,null,null,null,null,null,null,null,null],["Mihai Eminescu",12,"Nu în public","În fața Universității","Grădina Botanică","Oscar Han",1930,46.76746702,23.59118075,"estetic","Grădina Botanică",1930,46.76093949,23.58763963,null,null,null,null,null,null,null,null,null,null],["Monumentul tanchiștilor sovietici",13,"Nu în public","În spatele Cat. ortodoxe","Cimitirul eroilor","Ing. locotenent major Nosov",1945,46.77230768,23.59610724,"regim","Cimitirul Eroilor",1990,46.75879222,23.59766183,null,null,null,null,null,null,null,null,null,null],["Emil Racoviță",16,"Da","În centrul Str. Kogălniceanu","În fața liceului E. Racoviță","necunoscut",1993,46.7679177,23.59435578,"urbanism","În fața Liceului Emil Racoviță",2024,46.76791965,23.59390819,null,null,null,null,null,null,null,null,null,null],["Alexandru Ioan Cuza",18,"Da","B-dul. 21 Decembrie x Str. Cuza Vodă ","În centrul scuarului","Marcel Voinea",1999,46.77259882,23.59520435,"urbanism","În centrul scuarului",2020,46.77274086,23.59489974,null,null,null,null,null,null,null,null,null,null],["Gardianul Carpaților",9,"Nu","P-ța Unirii x B-dul Eroilor","(dispărut)","Szaszak Ferenc",1915,46.76934052,23.59100711,"regim",null,1918,46.76934052,23.59100711,null,null,null,null,null,null,null,null,null,null],["Obeliscul eroilor sovietici",14,"Nu","În fața Teatrului Național","(dispărut)","Proiectare: Ing. locotenent major Nosov",1947,46.77087366,23.59702072,"regim",null,1970,46.77087366,23.59702072,null,null,null,null,null,null,null,null,null,null],["Piatra de temelie pentru replica Columnei lui Traian",17,"Nu","P-ța Unirii","(dispărut)","Realizare: Firma „Napoca Construcții”",1998,46.76923099,23.59009067,"urbanism",null,2004,46.76923099,23.59009067,null,null,null,null,null,null,null,null,null,null]],"fields":[{"name":"Titlu","type":"string","format":"","analyzerType":"STRING"},{"name":"vechime","type":"integer","format":"","analyzerType":"INT"},{"name":"există","type":"string","format":"","analyzerType":"STRING"},{"name":"loc inițial","type":"string","format":"","analyzerType":"STRING"},{"name":"loc final","type":"string","format":"","analyzerType":"STRING"},{"name":"artist","type":"string","format":"","analyzerType":"STRING"},{"name":"datare","type":"integer","format":"","analyzerType":"INT"},{"name":"lat A","type":"real","format":"","analyzerType":"FLOAT"},{"name":"lon A","type":"real","format":"","analyzerType":"FLOAT"},{"name":"motiv A","type":"string","format":"","analyzerType":"STRING"},{"name":"loc B","type":"string","format":"","analyzerType":"STRING"},{"name":"an B","type":"integer","format":"","analyzerType":"INT"},{"name":"lat B","type":"real","format":"","analyzerType":"FLOAT"},{"name":"lon B","type":"real","format":"","analyzerType":"FLOAT"},{"name":"motiv B","type":"string","format":"","analyzerType":"STRING"},{"name":"loc C","type":"string","format":"","analyzerType":"STRING"},{"name":"an C","type":"integer","format":"","analyzerType":"INT"},{"name":"lat C","type":"real","format":"","analyzerType":"FLOAT"},{"name":"lon C","type":"real","format":"","analyzerType":"FLOAT"},{"name":"motiv C","type":"string","format":"","analyzerType":"STRING"},{"name":"loc D","type":"string","format":"","analyzerType":"STRING"},{"name":"an D","type":"integer","format":"","analyzerType":"INT"},{"name":"lat D","type":"real","format":"","analyzerType":"FLOAT"},{"name":"lon D","type":"real","format":"","analyzerType":"FLOAT"}]}}];
            const config = {"version":"v1","config":{"visState":{"filters":[{"dataId":["-5hx2pt"],"id":"ntaqzh6a","name":["datare"],"type":"range","value":[1740,1999],"plotType":{"type":"histogram"},"animationWindow":"free","yAxis":null,"view":"side","speed":1,"enabled":true},{"dataId":["-5hx2pt"],"id":"ydv9dl9nd","name":["Titlu"],"type":"multiSelect","value":[],"plotType":{"type":"histogram"},"animationWindow":"free","yAxis":null,"view":"side","speed":1,"enabled":true}],"layers":[{"id":"fsjl039m","type":"point","config":{"dataId":"-5hx2pt","columnMode":"points","label":"a","color":[255,203,153],"highlightColor":[252,242,26,255],"columns":{"lat":"lat A","lng":"lon A"},"isVisible":true,"visConfig":{"radius":10,"fixedRadius":false,"opacity":0.8,"outline":true,"thickness":3.5,"strokeColor":null,"colorRange":{"name":"Uber Viz Qualitative 4","type":"qualitative","category":"Uber","colors":["#12939A","#DDB27C","#88572C","#FF991F","#F15C17","#223F9A","#DA70BF","#125C77","#4DC19C","#776E57","#17B8BE","#F6D18A","#B7885E","#FFCB99","#F89570","#829AE3","#E79FD5","#1E96BE","#89DAC1","#B3AD9E"]},"strokeColorRange":{"name":"Custom Palette","type":"custom","category":"Custom","colors":["#0d2160","#CA168E","#F9E200"]},"radiusRange":[0,50],"filled":true,"billboard":false,"allowHover":true,"showNeighborOnHover":false,"showHighlightColor":true},"hidden":false,"textLabel":[{"field":{"name":"datare","type":"integer"},"color":[25,20,16],"size":16,"offset":[0,0],"anchor":"middle","alignment":"center","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"Titlu","type":"string"},"colorScale":"ordinal","strokeColorField":{"name":"există","type":"string"},"strokeColorScale":"ordinal","sizeField":null,"sizeScale":"linear"}},{"id":"zbay0en","type":"point","config":{"dataId":"-5hx2pt","columnMode":"points","label":"b","color":[248,149,112],"highlightColor":[252,242,26,255],"columns":{"lat":"lat B","lng":"lon B"},"isVisible":true,"visConfig":{"radius":10,"fixedRadius":false,"opacity":0.8,"outline":false,"thickness":2,"strokeColor":null,"colorRange":{"name":"Uber Viz Qualitative 4","type":"qualitative","category":"Uber","colors":["#12939A","#DDB27C","#88572C","#FF991F","#F15C17","#223F9A","#DA70BF","#125C77","#4DC19C","#776E57","#17B8BE","#F6D18A","#B7885E","#FFCB99","#F89570","#829AE3","#E79FD5","#1E96BE","#89DAC1","#B3AD9E"]},"strokeColorRange":{"name":"Global Warming","type":"sequential","category":"Uber","colors":["#5A1846","#900C3F","#C70039","#E3611C","#F1920E","#FFC300"]},"radiusRange":[0,50],"filled":true,"billboard":false,"allowHover":true,"showNeighborOnHover":false,"showHighlightColor":true},"hidden":false,"textLabel":[{"field":{"name":"an B","type":"integer"},"color":[112,108,99],"size":16,"offset":[0,0],"anchor":"end","alignment":"center","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"Titlu","type":"string"},"colorScale":"ordinal","strokeColorField":null,"strokeColorScale":"quantile","sizeField":null,"sizeScale":"linear"}},{"id":"2y5vkt2","type":"point","config":{"dataId":"-5hx2pt","columnMode":"points","label":"c","color":[130,154,227],"highlightColor":[252,242,26,255],"columns":{"lat":"lat C","lng":"lon C"},"isVisible":true,"visConfig":{"radius":10,"fixedRadius":false,"opacity":0.8,"outline":false,"thickness":2,"strokeColor":null,"colorRange":{"name":"Uber Viz Qualitative 4","type":"qualitative","category":"Uber","colors":["#12939A","#DDB27C","#88572C","#FF991F","#F15C17","#223F9A","#DA70BF","#125C77","#4DC19C","#776E57","#17B8BE","#F6D18A","#B7885E","#FFCB99","#F89570","#829AE3","#E79FD5","#1E96BE","#89DAC1","#B3AD9E"]},"strokeColorRange":{"name":"Global Warming","type":"sequential","category":"Uber","colors":["#5A1846","#900C3F","#C70039","#E3611C","#F1920E","#FFC300"]},"radiusRange":[0,50],"filled":true,"billboard":false,"allowHover":true,"showNeighborOnHover":false,"showHighlightColor":true},"hidden":false,"textLabel":[{"field":{"name":"an C","type":"integer"},"color":[112,108,99],"size":16,"offset":[0,0],"anchor":"start","alignment":"center","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"Titlu","type":"string"},"colorScale":"ordinal","strokeColorField":null,"strokeColorScale":"quantile","sizeField":null,"sizeScale":"linear"}},{"id":"k8ok404p","type":"point","config":{"dataId":"-5hx2pt","columnMode":"points","label":"d","color":[231,159,213],"highlightColor":[252,242,26,255],"columns":{"lat":"lat D","lng":"lon D"},"isVisible":true,"visConfig":{"radius":10,"fixedRadius":false,"opacity":0.8,"outline":false,"thickness":2,"strokeColor":null,"colorRange":{"name":"Uber Viz Qualitative 4","type":"qualitative","category":"Uber","colors":["#12939A","#DDB27C","#88572C","#FF991F","#F15C17","#223F9A","#DA70BF","#125C77","#4DC19C","#776E57","#17B8BE","#F6D18A","#B7885E","#FFCB99","#F89570","#829AE3","#E79FD5","#1E96BE","#89DAC1","#B3AD9E"]},"strokeColorRange":{"name":"Global Warming","type":"sequential","category":"Uber","colors":["#5A1846","#900C3F","#C70039","#E3611C","#F1920E","#FFC300"]},"radiusRange":[0,50],"filled":true,"billboard":false,"allowHover":true,"showNeighborOnHover":false,"showHighlightColor":true},"hidden":false,"textLabel":[{"field":{"name":"an D","type":"integer"},"color":[112,108,99],"size":16,"offset":[0,0],"anchor":"middle","alignment":"bottom","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"Titlu","type":"string"},"colorScale":"ordinal","strokeColorField":null,"strokeColorScale":"quantile","sizeField":null,"sizeScale":"linear"}},{"id":"uvo07qq","type":"arc","config":{"dataId":"-5hx2pt","columnMode":"points","label":"a -> b arc","color":[146,38,198],"highlightColor":[252,242,26,255],"columns":{"lat0":"lat A","lng0":"lon A","lat1":"lat B","lng1":"lon B"},"isVisible":true,"visConfig":{"opacity":0.8,"thickness":2,"colorRange":{"name":"Custom Palette","type":"custom","category":"Custom","colors":["#377eb8","#e41a1c","#4daf4a"]},"sizeRange":[0,10],"targetColor":null},"hidden":false,"textLabel":[{"field":null,"color":[255,255,255],"size":18,"offset":[0,0],"anchor":"start","alignment":"center","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"motiv A","type":"string"},"colorScale":"ordinal","sizeField":null,"sizeScale":"linear"}},{"id":"o0d2u4b","type":"arc","config":{"dataId":"-5hx2pt","columnMode":"points","label":"b -> c arc","color":[146,38,198],"highlightColor":[252,242,26,255],"columns":{"lat0":"lat B","lng0":"lon B","lat1":"lat C","lng1":"lon C"},"isVisible":true,"visConfig":{"opacity":0.8,"thickness":2,"colorRange":{"name":"Custom Palette","type":"custom","category":"Custom","colors":["#e41a1c","#4daf4a","#377eb8"]},"sizeRange":[0,10],"targetColor":null},"hidden":false,"textLabel":[{"field":null,"color":[255,255,255],"size":18,"offset":[0,0],"anchor":"start","alignment":"center","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"motiv B","type":"string"},"colorScale":"ordinal","sizeField":null,"sizeScale":"linear"}},{"id":"nur958l","type":"arc","config":{"dataId":"-5hx2pt","columnMode":"points","label":"c -> d arc","color":[146,38,198],"highlightColor":[252,242,26,255],"columns":{"lat0":"lat C","lng0":"lon C","lat1":"lat D","lng1":"lon D"},"isVisible":true,"visConfig":{"opacity":0.8,"thickness":2,"colorRange":{"name":"Custom Palette","type":"custom","category":"Custom","colors":["#4daf4a","#e41a1c","#377eb8"]},"sizeRange":[0,10],"targetColor":null},"hidden":false,"textLabel":[{"field":null,"color":[255,255,255],"size":18,"offset":[0,0],"anchor":"start","alignment":"center","outlineWidth":0,"outlineColor":[255,0,0,255],"background":false,"backgroundColor":[0,0,200,255]}]},"visualChannels":{"colorField":{"name":"motiv C","type":"string"},"colorScale":"ordinal","sizeField":null,"sizeScale":"linear"}}],"effects":[],"interactionConfig":{"tooltip":{"fieldsToShow":{"-5hx2pt":[{"name":"Titlu","id":"Titlu","displayName":"Titlu","format":"","fieldIdx":0,"type":"string","analyzerType":"STRING"},{"name":"artist","id":"artist","displayName":"artist","format":"","fieldIdx":5,"type":"string","analyzerType":"STRING"},{"name":"datare","id":"datare","displayName":"datare","format":"","fieldIdx":6,"type":"integer","analyzerType":"INT"},{"name":"există","id":"există","displayName":"există","format":"","fieldIdx":2,"type":"string","analyzerType":"STRING"},{"name":"loc inițial","id":"loc inițial","displayName":"loc inițial","format":"","fieldIdx":3,"type":"string","analyzerType":"STRING"},{"name":"loc final","id":"loc final","displayName":"loc final","format":"","fieldIdx":4,"type":"string","analyzerType":"STRING"}]},"compareMode":false,"compareType":"absolute","enabled":true},"brush":{"size":0.5,"enabled":false},"geocoder":{"enabled":false},"coordinate":{"enabled":false}},"layerBlending":"normal","overlayBlending":"normal","splitMaps":[],"animationConfig":{"currentTime":null,"speed":1},"editor":{"features":[],"visible":true}},"mapState":{"bearing":0,"dragRotate":false,"latitude":46.7674802650774,"longitude":23.581756489141046,"pitch":0,"zoom":13.98061402156002,"isSplit":false,"isViewportSynced":true,"isZoomLocked":false,"splitMapViewports":[]},"mapStyle":{"styleType":"positron-nolabels","topLayerGroups":{},"visibleLayerGroups":{"label":true,"road":true,"border":false,"building":true,"water":true,"land":true,"3d building":false},"threeDBuildingColor":[232.7874787737094,232.7874787737094,230.92517894351974],"backgroundColor":[0,0,0],"mapStyles":{}}}};

            const loadedData = keplerGl.KeplerGlSchema.load(
              datasets,
              config
            );

            store.dispatch(keplerGl.addDataToMap({
              datasets: loadedData.datasets,
              config: loadedData.config,
              options: {
                centerMap: false
              }
            }));
          }(KeplerGl, store))
        </script>
      </body>
    </html>
  