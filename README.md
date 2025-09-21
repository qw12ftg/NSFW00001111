cloudstream-plugins/
├── plugins.json
└── TestPlugin/
    ├── plugin.json
    └── plugin.apk ()

{
  "name": "HentaiBrosPlugin.csx",
  "description": "This is a test plugin",
  "version": 1,
  "language": "en",
  "url": "",
  "authors": ["YourName"]
}
https://raw.githubusercontent.com/ukuh22195@gmail.com/cloudstream-plugins/main/plugins.json
// HentaiBrosPlugin.csx
class HentaiBrosPlugin : MainAPI() {
    override var mainUrl = "https://hentaibros.net"

    override var name = "HentaiBros"
    override var lang = "ar"

    override val supportedTypes = setOf(TvType.Movie)

    override suspend fun load(url: String): LoadResponse? {
        val document = app.get(url).document

        return newMovieLoadResponse(
            document.select("h1.entry-title").text(),
            url,
            TvType.Movie,
            url
        ) {
            posterUrl = document.select("div.entry-thumbnail img").attr("src")
            year = document.select("span.year").text().toIntOrNull()
        }
    }

    override suspend fun search(query: String): List<SearchResponse> {
        val searchUrl = "$mainUrl/?s=$query"
        val document = app.get(searchUrl).document

        return document.select("article h2.entry-title a").map {
            MovieSearchResponse(
                it.text(),
                it.attr("href"),
                TvType.Movie,
                it.select("img").attr("src")
            )
        }
    }
}
