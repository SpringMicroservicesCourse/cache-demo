# Spring Boot Cache 示範專案 - 咖啡訂單系統 ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

本專案是一個展示 **Spring Boot Cache 快取機制** 的咖啡訂單管理系統，透過實際的業務場景來演示如何有效運用快取技術提升應用程式效能。系統實作了咖啡商品管理和訂單處理功能，並整合了 Spring Cache 抽象層來實現智慧快取策略。

- **核心功能**：咖啡商品查詢、訂單建立與狀態管理、快取優化
- **解決問題**：減少資料庫查詢次數，提升高頻訪問資料的回應速度
- **主要特色**：展示 `@Cacheable`、`@CacheEvict` 等註解的實際應用
- **目標使用者**：學習 Spring Boot Cache 機制的開發者與架構師

> 💡 **為什麼選擇此專案？**
> - 以真實業務場景展示快取設計模式與最佳實踐
> - 整合現代 Java 開發工具鏈，符合企業級開發標準
> - 提供完整的快取策略實作範例，可直接應用於生產環境

### 🎯 專案特色

- **智慧快取策略**：使用 Spring Cache 註解實現透明化快取管理
- **金額精確處理**：整合 Joda Money 確保財務計算的精確性
- **現代化資料處理**：採用 Jakarta Persistence API 與 Hibernate 6.x
- **程式碼簡潔性**：大量使用 Lombok 減少樣板程式碼
- **記憶體資料庫**：使用 H2 Database 快速建置開發與測試環境

## 技術棧

### 核心框架
- **Spring Boot 3.4.5** - 主要應用框架，提供自動配置與微服務基礎
- **Spring Data JPA** - 資料持久層框架，簡化資料庫操作
- **Spring Cache** - 快取抽象層，支援多種快取實作
- **Hibernate 6.x** - ORM 框架，處理物件關聯對應

### 開發工具與輔助
- **Lombok** - 自動產生 getter/setter 等樣板程式碼
- **Joda Money** - 專業金額處理函式庫，確保財務計算精確度
- **H2 Database** - 記憶體資料庫，適合開發與測試環境
- **Maven** - 專案建置與相依性管理工具

## 專案結構

```
cache-demo/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── tw/fengqing/spring/springbucks/
│   │   │       ├── SpringBucksApplication.java    # 主程式進入點
│   │   │       ├── model/                         # 實體類別 (Entity)
│   │   │       │   ├── BaseEntity.java          # 基礎實體 (ID、時戳)
│   │   │       │   ├── Coffee.java              # 咖啡商品實體
│   │   │       │   ├── CoffeeOrder.java         # 咖啡訂單實體
│   │   │       │   ├── OrderState.java          # 訂單狀態列舉
│   │   │       │   └── MoneyConverter.java      # 金額轉換器
│   │   │       ├── repository/                   # 資料存取層 (Repository)
│   │   │       │   ├── CoffeeRepository.java    # 咖啡商品資料存取
│   │   │       │   └── CoffeeOrderRepository.java # 訂單資料存取
│   │   │       └── service/                      # 業務邏輯層 (Service)
│   │   │           ├── CoffeeService.java       # 咖啡服務 (含快取)
│   │   │           └── CoffeeOrderService.java  # 訂單服務
│   │   └── resources/
│   │       ├── application.properties            # 應用程式設定
│   │       └── schema.sql                       # 資料庫結構與初始資料
│   └── test/                                     # 測試程式碼
├── pom.xml                                       # Maven 專案設定
└── README.md                                     # 專案說明文件
```

## 快速開始

### 前置需求
- **Java 21** 或更高版本
- **Maven 3.6+** 建置工具
- **IDE** (推薦 IntelliJ IDEA 或 VS Code)

### 安裝與執行

1. **克隆此倉庫：**
```bash
git clone https://github.com/SpringMicroservicesCourse/cache-demo.git
```

2. **進入專案目錄：**
```bash
cd cache-demo
```

3. **編譯專案：**
```bash
mvn clean compile
```

4. **執行應用程式：**
```bash
mvn spring-boot:run
```

5. **觀察快取效果：**
   - 應用程式啟動後會自動執行示範程式碼
   - 觀察 console 輸出，可看到首次查詢會執行 SQL，後續查詢直接從快取取得
   - 快取刷新後會重新執行 SQL 查詢

## 進階說明

### 快取配置
```java
@CacheConfig(cacheNames = "coffee")  // 設定快取名稱
public class CoffeeService {
    
    @Cacheable  // 方法結果會被快取
    public List<Coffee> findAllCoffee() {
        return coffeeRepository.findAll();
    }
    
    @CacheEvict  // 清除指定快取
    public void reloadCoffee() {
        // 觸發快取清除，下次查詢會重新載入資料
    }
}
```

### 資料庫設定
```properties
# application.properties 主要設定
spring.jpa.hibernate.ddl-auto=none          # 不自動建立資料表
spring.jpa.properties.hibernate.show_sql=true    # 顯示 SQL 語句
spring.jpa.properties.hibernate.format_sql=true  # 格式化 SQL 輸出
```

### 金額處理機制
```java
@Convert(converter = MoneyConverter.class)  // 使用自訂轉換器
private Money price;  // 使用 Joda Money 處理金額

// MoneyConverter 將 Money 物件轉換為資料庫的 Long 型態 (以分為單位)
// 例如：100.50 元 → 10050 分
```

## 參考資源

- [Spring Boot Cache 官方文件](https://docs.spring.io/spring-boot/docs/current/reference/html/io.html#io.caching)
- [Spring Framework Cache Abstraction](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#cache)
- [Joda Money 官方文件](https://www.joda.org/joda-money/)
- [Hibernate 6.x 官方文件](https://hibernate.org/orm/documentation/6.4/)

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目 | 說明 | 建議做法 |
|------|------|----------|
| 快取策略 | 避免快取穿透與雪崩 | 設定合理的快取過期時間與容錯機制 |
| 金額精確度 | 避免浮點數計算誤差 | 使用 Joda Money 以最小單位儲存 |
| 資料一致性 | 快取與資料庫同步 | 適時使用 @CacheEvict 清除過期快取 |
| 記憶體使用 | H2 為記憶體資料庫 | 應用程式重啟後資料會重置 |

### 🔒 最佳實踐指南

- **快取鍵設計**：使用具有業務意義且唯一的快取鍵值
- **快取粒度**：選擇適當的快取粒度，避免過細或過粗
- **監控機制**：建議整合快取命中率監控，優化快取策略
- **異常處理**：快取操作失敗時應有降級處理機制

## 核心程式碼說明

### 快取服務實作
```java
/**
 * 咖啡服務類別 - 展示 Spring Cache 的使用方式
 * 使用 @CacheConfig 統一設定快取名稱為 "coffee"
 */
@CacheConfig(cacheNames = "coffee")
public class CoffeeService {
    
    /**
     * 查詢所有咖啡商品
     * @Cacheable 註解表示此方法的結果會被快取
     * 首次調用會執行資料庫查詢，後續調用直接從快取回傳
     */
    @Cacheable
    public List<Coffee> findAllCoffee() {
        return coffeeRepository.findAll();
    }
    
    /**
     * 重新載入咖啡資料
     * @CacheEvict 註解會清除指定的快取
     * 下次調用 findAllCoffee() 時會重新查詢資料庫
     */
    @CacheEvict
    public void reloadCoffee() {
        // 此方法主要用於觸發快取清除
    }
}
```

## 授權說明

本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

## 關於我們

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。

## 聯繫我們

- **FB 粉絲頁**：[風清雲談 | Facebook](https://www.facebook.com/profile.php?id=61576838896062)
- **LinkedIn**：[linkedin.com/in/chu-kuo-lung](https://www.linkedin.com/in/chu-kuo-lung)
- **YouTube 頻道**：[雲談風清 - YouTube](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- **風清雲談 部落格**：[風清雲談](https://blog.fengqing.tw/)
- **電子郵件**：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

---

**📅 最後更新：2025-06-30**  
**👨‍💻 維護者：風清雲談團隊** 