# Prism for Claude Code

[Prism Pipeline 2](https://prism-pipeline.com) projesinde Claude Code ile çalışmak için eklenti.
Claude'a "Props altında hangi asset'lerin modeli yok?", "Lotus diye bir asset aç, modelleme task'ıyla",
"bu haftaki publish'leri özetle" gibi şeyler söyleyebilirsin. Prism'de hiçbir pencere açılmaz.

Eklentide iki şey var:

- **Prism MCP:** Claude'un Prism'de bakıp değişiklik yapabildiği araçlar (listesi: [PrismMcp/TOOLS.md](PrismMcp/TOOLS.md))
- **prism skill'i:** Claude'a bu araçları nasıl güvenli kullanacağını anlatır. Var olan bir şeyi değiştirmeden ya da
  silmeden önce sana ne değişeceğini gösterip onay ister.

## Gerekenler

- Windows
- **Prism 2** kurulu (varsayılan yer: `C:\Program Files\Prism2`), içinde kullanıcı adın girilmiş
- **Claude Code**

## Kurulum

Claude Code'u aç ve sırayla şunları yaz:

```
/plugin marketplace add metinxsezdin/prism-claude-plugin
/plugin install prism@prism-claude-plugin
```

Sonra Claude Code'u kapatıp yeniden aç. Denemek için Claude'a şunu yaz: `Prism'e bağlı mısın?`
Claude Prism sürümünü, kullanıcı adını ve projeni söylemeli.

**Prism başka bir klasöre kuruluysa** (örneğin `D:\Prism2`) kurulumdan önce bir kere PowerShell'de şunu çalıştır,
sonra bilgisayardan çıkıp tekrar gir:

```powershell
[Environment]::SetEnvironmentVariable("PRISM_ROOT", "D:\Prism2", "User")
```

**Daha önce Prism Unity eklentisinin `Install.bat` dosyasını çalıştırdıysan** Prism araçları sende zaten var.
Bu eklentiyi kurarsan aynı araçlar iki kere görünür. İkisinden birini seç. Eskisini kaldırmak için:
`claude mcp remove prism --scope user`

## Güncelleme

```
/plugin marketplace update prism-claude-plugin
```

## Sorun olursa

- **Claude Prism araçlarını görmüyor:** `/mcp` yaz. Listede `prism` yoksa ya da "failed" yazıyorsa Prism'in kurulu
  olduğu klasörü kontrol et (yukarıdaki `PRISM_ROOT`).
- **"Prism user name" istiyor:** Prism'i aç, Settings > User kısmına adını yaz.
- **"drive is not reachable":** projenin bulunduğu ağ sürücüsü bağlı değil.
- Claude'un yaptığı her değişiklik `Belgeler\Prism2\PrismMcp.log` dosyasına yazılır.

Bu repo [prism_unity_plugin](https://github.com/metinxsezdin/prism_unity_plugin) reposundaki Prism MCP'nin bir kopyasıdır.
