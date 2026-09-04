# Knowledge hub

Đây là nơi lưu trữ tập trung các nguồn đang follow, nguồn dùng để discovery, tool/product, keyword và các mục cần đưa vào research sau này.

## Vai trò của folder này

- `knowledge/` là **source library** và điểm vào để tìm kiếm.
- `knowledge/inbox.md` là nơi ghi nhanh nguồn mới trước khi phân loại.
- `knowledge/sources/` là catalog đã được phân loại.
- `knowledge/search-keywords.md` là query bank để tìm thêm nội dung.
- `research/notes/` là nơi ghi phân tích sau khi đã đọc/nghiên cứu.
- `research/sources.md` là registry các nguồn được dùng làm bằng chứng cho claim research.

Một kênh YouTube hoặc một bài đăng cộng đồng là nguồn discovery/learning, không tự động là nguồn evidence. Khi một thông tin được dùng để kết luận, hãy tạo research note có provenance và đưa nguồn liên quan vào `research/sources.md`.

## Quy trình lưu trữ

```text
Discover → Inbox → Normalize URL → Tag → Verify → Catalog → Research note → Evidence registry
```

## Metadata tối thiểu

Mỗi entry nên có:

```yaml
source_type: youtube | news | community | social | tool | product
role: discovery | learning | evidence | benchmark | implementation
topics: []
status: following | monitor | backlog | archived
trust_level: official | primary | secondary | community
last_checked: YYYY-MM-DD
```

## Điều hướng

- [Master index](index.md)
- [Taxonomy](taxonomy.md)
- [Search keywords](search-keywords.md)
- [Inbox](inbox.md)
- [YouTube channels](sources/youtube-channels.md)
- [News and community](sources/news-and-community.md)
- [Tools and products](sources/tools-and-products.md)

