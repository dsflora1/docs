---

copyright:
  years: 2015, 2017

lastupdated: "2017-02-16"

---


{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:screen: .screen}

# Cloud Foundry-App-Protokolle über die Befehlszeilenschnittstelle (CLI) analysieren
{: #analyzing_logs_cli}

In {{site.data.keyword.Bluemix}} können Sie Protokolle über die Befehlszeilenschnittstelle (CLI) mithilfe des Befehls **cf logs** anzeigen, filtern und analysieren. Über die Befehlszeile können Protokolle programmgesteuert verwaltet werden.
{:shortdesc}

Verwenden Sie den Befehl **cf logs**, um Protokolle aus einer Cloud Foundry-App und aus den Systemkomponenten, die mit der App interagieren, anzuzeigen, wenn Sie die App in {{site.data.keyword.Bluemix_notm}} bereitstellen. Der Befehl **cf logs** zeigt die STDOUT- und STDERR-Protokolldatenströme einer Cloud Foundry-Anwendung an.

Zum Anzeigen von für Sie relevanten Protokollen oder zum Ausschließen des Inhalts, den Sie nicht anzeigen möchten, können Sie den Befehl **cf logs** mit Filteroptionen wie **cut** und **grep** in der CF-Befehlszeilenschnittstelle verwenden:

* Informationen zum Anzeigen von Protokollen für eine Cloud Foundry-App: [Protokoll für eine Cloud Foundry-App anzeigen](logging_view_cli.html#full_log_cli).
* Informationen zum Anzeigen der letzten Protokolleinträge für eine Cloud Foundry-App: [Letzte Protokolleinträge für eine Cloud Foundry-App anzeigen](logging_view_cli.html#tailing_log_cli).
* Informationen zum Anzeigen der Protokolleinträge für eine Cloud Foundry-App in einem bestimmten Zeitraum: [Abschnitt der Protokolle anzeigen](logging_view_cli.html#partial_log_cli).
* Informationen zum Anzeigen der Einträge in den Protokollen für eine Cloud Foundry-App, die bestimmte Schlüsselwörter enthalten: [Protokolleinträge mit bestimmten Schlüsselwörtern anzeigen](logging_view_cli.html#partial_by_keyword_log_cli).


## Protokoll für eine Cloud Foundry-App anzeigen
{: #full_log_cli}

Führen Sie die folgenden Schritte aus, um alle Protokolle anzuzeigen, die für eine Cloud Foundry-App verfügbar sind:

1. Öffnen Sie ein Terminal und melden Sie sich bei {{site.data.keyword.Bluemix}} an.

2. Führen Sie den folgenden Befehl über die Befehlszeile aus, um alle Protokolle anzuzeigen:

   <pre class="pre screen"><code>cf logs <var class="keyword varname">App-Name</var></code></pre>
   
   
## Letzte Protokolleinträge für eine Cloud Foundry-App anzeigen
{: #tailing_log_cli}

Führen Sie die folgenden Schritte aus, um die letzten Protokolle anzuzeigen, die für eine Cloud Foundry-App verfügbar sind: 

1. Öffnen Sie ein Terminal und melden Sie sich bei {{site.data.keyword.Bluemix}} an.

2. Führen Sie den folgenden Befehl über die Befehlszeile aus, um alle Protokolle anzuzeigen:

     <pre class="pre screen"><code>cf logs <var class="keyword varname">App-Name</var> --recent</code></pre>

<div class="note tip"><span class="tiptitle">Tipp:</span> Wenn Sie den Befehl <span class="keyword cmdname">cf push</span> oder <span class="keyword cmdname">cf start</span> in einem Befehlszeilenfenster ausführen, können Sie <samp class="ph codeph">cf logs App-Name --recent</samp> in einem anderen Befehlszeilenfenster eingeben, um die Protokolle in Echtzeit anzuzeigen. </div>


## Abschnitt eines Cloud Foundry-Protokolls anzeigen
{: #partial_log_cli}

Führen Sie die folgenden Schritte aus, um einen Teil der Protokolle anzuzeigen, die für eine Cloud Foundry-App in einem Zeitraum verfügbar sind: 

1. Öffnen Sie ein Terminal und melden Sie sich bei {{site.data.keyword.Bluemix}} an.

2. Führen Sie den folgenden Befehl über die Befehlszeile aus, um alle Protokolle anzuzeigen:

    <pre class="pre screen"><code>cf logs <var class="keyword varname">App-Name</var> --recent  | cut -c 29-40,46-</code></pre>
    
    Für weitere Informationen zur Option **cut** geben Sie **cut --help** ein.


## Protokolleinträge mit bestimmten Schlüsselwörtern anzeigen
{: #partial_by_keyword_log_cli}

Führen Sie die folgenden Schritte aus, um Protokolleinträge anzuzeigen, die bestimmte Schlüsselwörter enthalten, für eine Cloud Foundry-App anzuzeigen:

1. Öffnen Sie ein Terminal und melden Sie sich bei {{site.data.keyword.Bluemix}} an.

2. Führen Sie den folgenden Befehl über die Befehlszeile aus, um alle Protokolle anzuzeigen:

    <pre class="pre screen"><code>cf logs <var class="keyword varname">App-Name</var> --recent | grep '<var class="keyword varname">Schlüsselwort</var>'</code></pre>
    

Protokolleinträge, die das Schlüsselwort **APP** enthalten, können Sie beispielsweise mithilfe des folgenden Befehls anzeigen: 

<pre class="pre screen"><code>cf logs App-Name --recent | grep '\[App'
</code></pre>

Für weitere Informationen zur Option **grep** geben Sie **grep --help** ein.


## Cloud Foundry-Anwendungsprotokolle
{: #cf_app_logs_cli}

Die folgenden Protokolle sind für eine Cloud Foundry-Anwendung verfügbar, wenn Sie sie in {{site.data.keyword.Bluemix}} bereitgestellt haben:

<dl><dt><strong>buildpack.log</strong></dt>
<dd>
<p>Diese Protokolldatei zeichnet differenzierte Informationsereignisse für das Debugging auf. Sie können dieses Protokoll verwenden, um Probleme bei der Ausführung des Buildpacks zu beheben.</p>

<p>Wenn Daten in der Datei <span class="ph filepath">buildpack.log</span> generiert werden sollen, müssen Sie die Traceerstellung für Buildpacks aktivieren, indem Sie den folgenden Befehl ausführen:</p>

   <pre class="pre">cf set-env <var class="keyword varname">App-Name</var> JBP_LOG_LEVEL DEBUG</pre>
   
<p>Geben Sie den folgenden Befehl ein, um dieses Protokoll anzuzeigen:</p>

    <pre class="pre">cf files <var class="keyword varname">App-Name</var> app/.buildpack-diagnostics/buildpack.log</pre>

</dd>

<dt><strong>staging_task.log</strong></dt>
<dd><p>Diese Protokolldatei zeichnet Nachrichten nach den wichtigsten Schritten der Staging-Task auf. Mithilfe dieses Protokolls können Sie Probleme beim Staging beheben.</p>

<p>Geben Sie den folgenden Befehl ein, um dieses Protokoll anzuzeigen:</p>

    <pre class="pre">cf files <var class="keyword varname">App-Name</var> logs/staging_task.log</pre>
</dd>
</dl>

**Hinweis:** Informationen zur Aktivierung der Anwendungsprotokollierung finden Sie unter [Laufzeitfehler beheben](/docs/debug/index.html#debugging-runtime-errors).

