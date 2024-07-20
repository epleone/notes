# 装饰器(Decorator)模式

## 实现一

这种写法的好处是接口和实现是一致的。就是调用的时候需要多包一层。

``` c++

class BaseEngine {

public:
    BaseEngine(Handle pHandle, std::string module_name = "BaseEngine", long err_base = 0) :
        m_pHandle(pHandle),
        m_module_name(module_name),
        m_err_base{ err_base }{}

    virtual ~BaseEngine() {}

    virtual void init() = 0;
    virtual void uninit() = 0;
    virtual void run() = 0;
    virtual void reset() = 0;


    BaseEngine(const BaseEngine&) = delete;
    BaseEngine& operator=(const BaseEngine&) = delete;

public:
    Handle m_pHandle = nullptr;
    std::string m_module_name;
    long	m_err_base;	
};


// Timer, Measure the performance of algorithm modules.
class TimerEngine : public BaseEngine {

public:
    BaseEngine* m_pEngine = nullptr;

public:
    TimerEngine(BaseEngine* pEngine) :
        BaseEngine(pEngine->m_pHandle, "Timer" + pEngine->m_module_name, pEngine->m_err_base),
        m_pEngine(pEngine) {}

    ~TimerEngine()
    {
        delete m_pEngine;
        m_pEngine = nullptr;
    }

    long init() override
    {
        if (!m_pEngine)
            return ERR_INVALID_PARAM;

        auto start = ArcVI_GetTime();
        print_log("\t%-20s: init.\n", m_pEngine->m_module_name.c_str());
        res = m_pEngine->init();
        print_log("%-20s: init Over, costs %f ms. res=%d.\n", m_pEngine->m_module_name.c_str(), ArcVI_GetTime() - start, res);

        return res;
    }

    long uninit() override
    {
        if (!m_pEngine)
            return ERR_INVALID_PARAM;

        auto start = ArcVI_GetTime();
        print_log("%-20s: free.\n", m_pEngine->m_module_name.c_str());
        res = m_pEngine->uninit();
        print_log("%-20s: free Over, costs %f ms. res=%d.\n", m_pEngine->m_module_name.c_str(), ArcVI_GetTime() - start, res);

        return res;
    }


    long run() override
    {
        if (!m_pEngine)
            return ERR_INVALID_PARAM;

        auto start = ArcVI_GetTime();
        print_log("%-20s: run.\n", m_pEngine->m_module_name.c_str());
        res = m_pEngine->run();
        print_log("%-20s: run Over, costs %f ms. res=%d.\n", m_pEngine->m_module_name.c_str(), ArcVI_GetTime() - start, res);

        return res;
    }


    long reset() override
    {
        if (!m_pEngine)
            return ERR_INVALID_PARAM;

        auto start = ArcVI_GetTime();
        print_log("%-20s: reset.\n", m_pEngine->m_module_name.c_str());
        res = m_pEngine->reset();
        print_log("%-20s: reset Over, costs %f ms. res=%d.\n", m_pEngine->m_module_name.c_str(), ArcVI_GetTime() - start, res);

        return res;
    }

};

template <typename EngineType>
EngineType* GetEngine(Handle pHandle)
{
    EngineType* eng = nullptr;

    for (auto& engine : pHandle->vecEngines)
    {
        TimerEngine* te = dynamic_cast<TimerEngine*>(engine);
        if (te)
        {
            eng = dynamic_cast<EngineType*>(te->m_pEngine);
        }
        else
        {
            eng = dynamic_cast<EngineType*>(engine);
        }

        if (eng)
        {
            break;
        }
    }

    return eng;
};

```

## 实现二

这种写法的好处逻辑更清晰，但是接口和实现是分离的，甚至可以分离成接口类和实现类, 类似`Pimpl`。

``` c++
struct MemMgr
{
    virtual ~MemMgr() {};
    virtual void* malloc(size_t size) = 0;
    virtual void  free(void* ptr) = 0;

private:
    virtual void* _malloc(size_t size) = 0;
    virtual void  _free(void* ptr) = 0;
};


/*  // 模板实现，写复杂了
template<class Derived>
struct MemDecorator : public MemManager
{
    void* mem_malloc(size_t size) override
    {
        net_print_log("malloc memsize: %fMB.", size * 1.0 / (1024 * 1024));
        return derived()._malloc(size);
    }

    void  mem_free(void* ptr) override
    {
        derived()._free(ptr);
    }

protected:
    // 将this指针转成Derived
    Derived& derived() { return *static_cast<Derived*>(this); }
    const Derived& derived() const { return *static_cast<const Derived*>(this); }
};
*/


struct MemDecorator : public MemMgr
{
    void* malloc(size_t size) override
    {
        log("malloc memsize: %fMB.", size * 1.0 / (1024 * 1024));
        return _malloc(size);
    }

    void  free(void* ptr) override
    {
	    log("free ...");
        _free(ptr);
    }

};


struct SysMgr final : public MemDecorator
{
private:
    void* _malloc(size_t size) override;
    void  _free(void* ptr) override;
};

```