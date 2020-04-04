<script>
  import axios from "axios";
  import { parse } from "node-html-parser";
  import { Button, Input } from "sveltestrap";
  import "bootswatch/dist/darkly/bootstrap.min.css";

  let querie = null;
  let page_number = 0;
  let result = "";
  let results = "";

  function handleEnter(event) {
    // key = event.key;
    let key_code = event.keyCode;
    if (key_code === 13) {
      handleNewSearch();
    }
  }

  export function handleSearch() {
    let pre_querie = querie.replace(/ /g, "+");
    // if (this.state.seriesChecked) {
    //   preQuerie = preQuerie + "&chs=on";
    // }
    // if (this.state.authorChecked) {
    //   preQuerie = preQuerie + "&cha=on";
    // }
    // if (this.state.bookChecked) {
    //   preQuerie = preQuerie + "&chb=on";
    // }
    // if (this.state.genreChecked) {
    //   preQuerie = preQuerie + '&chg=on'
    // }
    pre_querie = pre_querie + "&chb=on";
    const search_querie =
      "https://flibustasearch.herokuapp.com/http://flibusta.is/booksearch?page=" +
      page_number +
      "&ask=" +
      pre_querie;
    axios.get(search_querie).then(response => {
      result = response.data;
      refineResult();
      // this.setState({ result: response.data }, () => this.refineResult());
    });
  }

  export function handleNewSearch() {
    page_number = 0;
    handleSearch();
  }

  export function refineResult() {
    const result0 = String(result);
    const result1 = result0.substring(
      result.indexOf('<h1 class="title">Поиск книг</h1>') + 0
    );
    const result2 = result1.substring(
      0,
      result1.indexOf('<div id="sidebar-right" class="sidebar">')
    );
    const array1 = result2.split("\n");
    const array2 = array1.filter(String);
    let pagesTotal = array2.filter(elem => {
      if (
        elem.includes('class="pager"') ||
        elem.includes('<li class="pager-item"')
      ) {
        return true;
      }
    });
    // console.log(pagesTotal.length);
    // console.log(pagesTotal);

    const array3 = array2.filter(elem => {
      if (elem.includes('h1 class="title"')) {
        return false;
      }
      if (elem.includes("input type=submit value")) {
        return false;
      }
      if (elem.includes('<input type="checkbox"')) {
        return false;
      }
      if (elem.includes('<a href="http://fbsearch.ru">')) {
        return false;
      }
      if (elem.includes('class="pager')) {
        return false;
      }

      return true;
    });
    // const array6 = array3.map

    const array5 = array3.map((elem, index) => {
      if (elem.includes("<ul>")) {
        elem = elem.substr(elem.indexOf("<ul>") + 4);
        // console.log('found!')
      }
      elem = elem.replace(/<span style="background-color: #FFFCBB">/g, "");
      elem = elem.replace(/<\/span>/g, "");
      elem = elem.replace("<b>", "");
      elem = elem.replace("</b>", "");
      elem = elem.replace(/<a href="\//g, '<a href="http://flibusta.is/');

      if (elem.includes("flibusta.is/b")) {
        // elem = elem + elem.replace(elem.indexOf('/b/')+3, (elem.indexOf('/b/')+3)+'/fb2')

        elem =
          elem +
          elem.substring(elem.indexOf("<a href"), elem.indexOf('">')) +
          '/fb2">fb2 </a>' +
          elem.substring(elem.indexOf("<a href"), elem.indexOf('">')) +
          '/epub">epub </a>' +
          elem.substring(elem.indexOf("<a href"), elem.indexOf('">')) +
          '/mobi">mobi</a>';

        // + <a href="http://flibusta.is/b/530436/fb2">fb2</a>)
      }

      return elem;
    });

    const array6 = array5.map(elem => {
      elem = parse(elem);
      return elem.structuredText;
    });
    // console.log('array6 is', array6);
    // result = parse(array5)
    results = array6;
    // this.setState({ result2: array6, pagesTotal: pagesTotal.length / 2 });
    // result2 = array6
    pagesTotal = pagesTotal.length / 2;
  }
</script>

<svelte:head>
  <title>kraken book</title>
</svelte:head>
<svelte:window on:keydown={handleEnter} />

<div class="form-row">
  <div class="col-md-6">
    <div class="md-form form-group">
      <Input type="search" placeholder="Enter book name" bind:value={querie} />
    </div>
  </div>
  <div class="col-md-6">
    <div class="md-form form-group">
      <Button color="primary" on:click={handleNewSearch}>Search</Button>
    </div>
  </div>
</div>

{#each results as result}
  <div>{result}</div>
{/each}
