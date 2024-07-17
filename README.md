# One of my older user interface projects the code does not represent current skill just a user-interface design!

![User Interface](https://github.com/nolanpierce/user-interface/blob/main/image.png?raw=true)

## Overview

Welcome to the User Interface Project! This project aims to provide a seamless and intuitive interface for users, leveraging modern design principles and efficient coding practices.

## Features

- **User-Friendly Design**: The interface is designed with user experience in mind, ensuring ease of use and accessibility.
- **Responsive Layout**: Adapts to various screen sizes and devices.
- **Customizable Components**: Easily customizable UI components to fit different use cases.

### Notification animated 
```cpp
class NotificationSystem {
public:
    NotificationSystem() : notificationIdCounter(0) {}

    void AddNotification(const std::string& icon, const std::string& information, const std::string& message, int durationMs, ImU32 color) {
        auto now = std::chrono::steady_clock::now();
        auto endTime = now + std::chrono::milliseconds(durationMs);
        notifications.push_back({ notificationIdCounter++, icon, information, message, now, endTime, color });
    }

    void DrawNotifications() {
        auto now = std::chrono::steady_clock::now();

        std::sort(notifications.begin(), notifications.end(),
            [now](const Notification& a, const Notification& b) -> bool {
                float durationA = std::chrono::duration_cast<std::chrono::milliseconds>(a.endTime - a.startTime).count();
                float elapsedA = std::chrono::duration_cast<std::chrono::milliseconds>(now - a.startTime).count();
                float percentageA = (durationA - elapsedA) / durationA;

                float durationB = std::chrono::duration_cast<std::chrono::milliseconds>(b.endTime - b.startTime).count();
                float elapsedB = std::chrono::duration_cast<std::chrono::milliseconds>(now - b.startTime).count();
                float percentageB = (durationB - elapsedB) / durationB;

                return percentageA < percentageB;
            }
        );

        int currentNotificationPosition = 0;

        for (auto& notification : notifications) {
            if (now < notification.endTime) {
                float duration = std::chrono::duration_cast<std::chrono::milliseconds>(notification.endTime - notification.startTime).count();
                float elapsed = std::chrono::duration_cast<std::chrono::milliseconds>(now - notification.startTime).count();
                float percentage = (duration - elapsed) / duration * 100.0f;

                ShowNotification(currentNotificationPosition, notification.icon, notification.information, notification.message, percentage, notification.color);
                currentNotificationPosition++;
            }
        }

        notifications.erase(std::remove_if(notifications.begin(), notifications.end(),
            [now](const Notification& notification) { return now >= notification.endTime; }),
            notifications.end());
    }

private:
    std::vector<Notification> notifications;
    int notificationIdCounter;

    void ShowNotification(int position, const std::string& icon, const std::string& information, const std::string& message, float percentage, ImU32 color) {

        float duePercentage = 100.0f - percentage;
        float alpha = percentage > 10.0f ? 1.0f : percentage / 10.0f;

        PushFont(font::inter_medium_widget);
        SetNextWindowPos(ImVec2(GetIO().DisplaySize.x - (CalcTextSize(message.c_str()).x + CalcTextSize(information.c_str()).x + 70), 15 + (position * 55)), ImGuiCond_Always);
        SetNextWindowSize(ImVec2(CalcTextSize(message.c_str()).x + CalcTextSize(information.c_str()).x + 60, 40));
        PopFont();

        PushStyleVar(ImGuiStyleVar_WindowPadding, ImVec2(0, 0));
        PushStyleVar(ImGuiStyleVar_WindowRounding, 6.f);
        PushStyleColor(ImGuiCol_WindowBg, GetColorU32(c::element::info_bar::filling));

        ImGui::Begin(("##NOTIFY" + std::to_string(position)).c_str(), nullptr, ImGuiWindowFlags_NoDecoration | ImGuiWindowFlags_AlwaysAutoResize | ImGuiWindowFlags_NoMove);
        {
            ImVec2 pos = ImGui::GetWindowPos();

            PushStyleVar(ImGuiStyleVar_Alpha, 0.1f);
            GetForegroundDrawList()->AddRectFilled(pos + ImVec2(0, 0), pos + ImVec2(GetContentRegionMax().x * (duePercentage / 100.0f), GetContentRegionMax().y), ImGui::GetColorU32(color), c::element::rounding - 2);
            PopStyleVar();

            edited::RenderTextColor(font::icomoon, pos + ImVec2(0, 0), pos + ImVec2(40, 40), GetColorU32(color), icon.c_str(), ImVec2(0.52f, 0.52f));
            edited::RenderTextColor(font::inter_medium_widget, pos + ImVec2(40, 0), pos + ImVec2(GetContentRegionMax().x, 40), GetColorU32(color), message.c_str(), ImVec2(0.0f, 0.5f));
            edited::RenderTextColor(font::inter_medium_widget, pos + ImVec2(CalcTextSize(message.c_str()).x + 45, 0), pos + ImVec2(GetContentRegionMax().x, 40), GetColorU32(c::element::text_active), information.c_str(), ImVec2(0.0f, 0.5f));

        }
        End();

        PopStyleColor(1);
        PopStyleVar(2);
    }
};
```
### Renderer code snipper

```cpp
 WNDCLASSEXW wc = { sizeof(wc), CS_CLASSDC, WndProc, 0L, 0L, GetModuleHandle(nullptr), nullptr, nullptr, nullptr, nullptr, L"ImGui Example", nullptr };
 ::RegisterClassExW(&wc);
 HWND hwnd = ::CreateWindowW(wc.lpszClassName, L"Dear ImGui DirectX11 Example", WS_POPUP, 0, 0, 1920, 1080, nullptr, nullptr, wc.hInstance, nullptr);

 if (!CreateDeviceD3D(hwnd))
 {
     CleanupDeviceD3D();
     ::UnregisterClassW(wc.lpszClassName, wc.hInstance);
     return 1;
 }

 ::ShowWindow(hwnd, SW_SHOWDEFAULT);
 ::UpdateWindow(hwnd);

 IMGUI_CHECKVERSION();
 ImGui::CreateContext();
 ImGuiIO& io = ImGui::GetIO(); (void)io;
 io.ConfigFlags |= ImGuiConfigFlags_NavEnableKeyboard;   
 io.ConfigFlags |= ImGuiConfigFlags_NavEnableGamepad;    

 ImFontConfig cfg;
 cfg.FontBuilderFlags = ImGuiFreeTypeBuilderFlags_ForceAutoHint | ImGuiFreeTypeBuilderFlags_LightHinting | ImGuiFreeTypeBuilderFlags_LoadColor | ImGuiFreeTypeBuilderFlags_Bitmap;

 font::inter_medium_widget = io.Fonts->AddFontFromMemoryTTF(inter_medium, sizeof(inter_medium), 14.5f, &cfg, io.Fonts->GetGlyphRangesCyrillic());
 font::inter_medium_small = io.Fonts->AddFontFromMemoryTTF(inter_medium, sizeof(inter_medium), 14.f, &cfg, io.Fonts->GetGlyphRangesCyrillic());
 font::inter_medium_big = io.Fonts->AddFontFromMemoryTTF(inter_bold, sizeof(inter_bold), 25.f, &cfg, io.Fonts->GetGlyphRangesCyrillic());
 font::inter_bold = io.Fonts->AddFontFromMemoryTTF(inter_bold, sizeof(inter_bold), 17.f, &cfg, io.Fonts->GetGlyphRangesCyrillic());
 font::icomoon = io.Fonts->AddFontFromMemoryTTF(icomoon, sizeof(icomoon), 17.f, &cfg, io.Fonts->GetGlyphRangesCyrillic());
 font::icomoon_default = io.Fonts->AddFontFromMemoryTTF(icomoon, sizeof(icomoon), 20.f, &cfg, io.Fonts->GetGlyphRangesCyrillic());

 ImGui_ImplWin32_Init(hwnd);
 ImGui_ImplDX11_Init(g_pd3dDevice, g_pd3dDeviceContext);

 D3DX11_IMAGE_LOAD_INFO info; ID3DX11ThreadPump* pump{ nullptr };
 if (texture::preview == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, preview, sizeof(preview), &info, pump, &texture::preview, 0);
 if (texture::bg == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, background, sizeof(background), &info, pump, &texture::bg, 0);

 if (texture::ragebot == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, ragebot, sizeof(ragebot), &info, pump, &texture::ragebot, 0);
 if (texture::legitbot == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, legitbot, sizeof(legitbot), &info, pump, &texture::legitbot, 0);

 if (texture::visuals == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, visuals, sizeof(visuals), &info, pump, &texture::visuals, 0);
 if (texture::misc == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, misc, sizeof(misc), &info, pump, &texture::misc, 0);
 if (texture::world == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, world, sizeof(world), &info, pump, &texture::world, 0);
 if (texture::settings == nullptr) D3DX11CreateShaderResourceViewFromMemory(g_pd3dDevice, settings, sizeof(settings), &info, pump, &texture::settings, 0);

 bool show_demo_window = true;
 bool show_another_window = false;
 ImVec4 clear_color = ImVec4(0.45f, 0.55f, 0.60f, 1.00f);

 bool done = false;
 while (!done)
 {
     MSG msg;
     while (::PeekMessage(&msg, nullptr, 0U, 0U, PM_REMOVE))
     {
         ::TranslateMessage(&msg);
         ::DispatchMessage(&msg);
         if (msg.message == WM_QUIT)
             done = true;
     }
     if (done)
         break;

     if (g_ResizeWidth != 0 && g_ResizeHeight != 0)
     {
         CleanupRenderTarget();
         g_pSwapChain->ResizeBuffers(0, g_ResizeWidth, g_ResizeHeight, DXGI_FORMAT_UNKNOWN, 0);
         g_ResizeWidth = g_ResizeHeight = 0;
         CreateRenderTarget();
     }

     ImGui_ImplDX11_NewFrame();
     ImGui_ImplWin32_NewFrame();
     ImGui::NewFrame();
     {
         notificationSystem.DrawNotifications();

         GetBackgroundDrawList()->AddImage(texture::bg, ImVec2(0, 0), ImVec2(1920, 1080), ImVec2(0, 0), ImVec2(1, 1), ImColor(255, 255, 255, 255));

         ImGui::SetNextWindowSize(c::bg::size);

         ImGuiStyle* style = &ImGui::GetStyle();

         style->WindowPadding = ImVec2(10, 10);
         style->ItemSpacing = ImVec2(20, 20);
         style->WindowBorderSize = 0;
         style->ScrollbarSize = 10.f;

         ImGui::Begin("MENU", nullptr, ImGuiWindowFlags_NoDecoration | ImGuiWindowFlags_NoResize | ImGuiWindowFlags_NoFocusOnAppearing | ImGuiWindowFlags_NoBringToFrontOnFocus);
         {
             ImGuiStyle* style = &ImGui::GetStyle();

             const ImVec2& pos = GetWindowPos();
             const ImVec2& region = GetContentRegionMax();
             const ImVec2& spacing = style->ItemSpacing;

             ImGui::GetBackgroundDrawList()->AddRectFilledMultiColor(pos, pos + region, GetColorU32(c::bg::topbar, 1.f), GetColorU32(c::bg::topbar, 1.f), GetColorU32(c::black, 0.4f), GetColorU32(c::black, 0.4f), c::bg::rounding);

             ImGui::GetBackgroundDrawList()->AddRectFilled(pos + ImVec2(145, 60), pos + region, GetColorU32(c::bg::container), c::bg::rounding, ImDrawFlags_RoundCornersBottomRight);
             ImGui::GetBackgroundDrawList()->AddRectFilled(pos + ImVec2(145, 0), pos + ImVec2(region.x, 60), GetColorU32(c::bg::topbar), c::bg::rounding, ImDrawFlags_RoundCornersTopRight);
             ImGui::GetBackgroundDrawList()->AddLine(pos + ImVec2(0, 60), pos + ImVec2(region.x, 60), GetColorU32(c::bg::stroke), 1.f);

             ImGui::GetBackgroundDrawList()->AddCircle(pos + ImVec2(region.x - (60 / 2), (60 / 2)), 15, ImGui::GetColorU32(c::accent), 400.f, 2.f);

             ImGui::GetBackgroundDrawList()->PushClipRect(pos + ImVec2(145, 60), pos + ImVec2(region), true);
             ImGui::GetBackgroundDrawList()->AddShadowCircle(pos + ImVec2(145, 160), 100, ImGui::GetColorU32(c::accent, 0.15f), 400.f, ImVec2(0, 0), 0);
             ImGui::GetBackgroundDrawList()->AddShadowCircle(pos + ImVec2(region.x, 160), 100, ImGui::GetColorU32(c::accent, 0.15f), 400.f, ImVec2(0, 0), 0);
             ImGui::GetBackgroundDrawList()->AddShadowCircle(pos + ImVec2((145 + region.x) / 2, region.y), 100, ImGui::GetColorU32(c::accent, 0.15f), 400.f, ImVec2(0, 0), 0);
             ImGui::GetBackgroundDrawList()->PopClipRect();

             edited::RenderTextColor(font::inter_medium_big, pos, pos + ImVec2(145, 60), GetColorU32(c::element::text_active), "SKITZ.CC", ImVec2(0.5, 0.5));

             ImGui::SetCursorPos(ImVec2(10, 70));
             BeginGroup();
             {
                 ID3D11ShaderResourceView* pageShortcuts[6] = { texture::ragebot, texture::legitbot, texture::visuals, texture::misc, texture::world, texture::settings };
                 for (int i = 0; i < 6; i++) if (edited::Pages(i == var::page, var::pageNames[i], pageShortcuts[i], var::buttonSize)) var::page = i;
             }
             EndGroup();

             ImGui::SetCursorPos(ImVec2(region.x - (145 / 2), 60 - 35) / 2);

             edited::ICObutton(font::icomoon_default, "k", ImVec2(25, 35));

             if (edited::BeginPopupW("Languages", "Setting up languages in the menu."))
             {

                 edited::EndPopupW();
             }

             SameLine(0, 10);
             ImGui::InputTextEx("q", "Search", var::search, IM_ARRAYSIZE(var::search), ImVec2(220, 35), NULL);
             SameLine(0, 10);
             edited::ICObutton(font::icomoon_default, "m", ImVec2(25, 35));

             if (edited::BeginPopupW("preview", "Setting up the 'ESP PREVIEW' function."))
             {
                 edited::CheckboxWithPopup(false, "Activate the 'ESP Preview' window", "-", &esp_preview::active_esp_preview, NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                 edited::EndPopupW();
             }

             var::tab_alpha = ImClamp(var::tab_alpha + (15.f * ImGui::GetIO().DeltaTime * (var::page == var::active_tab ? 1.f : -1.f)), 0.f, 1.f);
             if (var::tab_alpha == 0.f && var::tab_add == 0.f) var::active_tab = var::page;

             var::tab_right = ImLerp(var::tab_right, var::page == var::active_tab ? 0.f : c::bg::size.x, ImGui::GetIO().DeltaTime * 13.f);
             var::tab_left = ImLerp(var::tab_left, var::interactive ? c::bg::size.x : 0.f, ImGui::GetIO().DeltaTime * 13.f);

             if (var::tab_alpha == 0.f) var::interactive = true;
             if (var::interactive && var::tab_left >= (c::bg::size.x - 0.6f)) var::interactive = false;

             ImGui::SetCursorPos(!var::interactive ? ImVec2(145.f + var::tab_right, 60.f) : ImVec2(-c::bg::size.x + (145.f + var::tab_left), 60.f));

             PushClipRect(pos + ImVec2(145, 60), pos + ImVec2(c::bg::size.x, c::bg::size.y), true);
             ImGui::BeginChild("CONTAINER", ImVec2(region.x - 145, region.y - 60));
             {
                 if (var::active_tab == 0)
                 {
                     ImGui::BeginGroup();
                     {
                         edited::BeginChild("General", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {
                             if (edited::CheckboxWithPopup(true, "Enabled", description_test.c_str(), &var::check[0], &var::key[0], &var::m[0], &var::value[0], true, true, var::col0, picker_flags))
                             {
                                 notificationSystem.AddNotification(ICON_WARNING, "Did you know?", "Artificium can memorize things.", 2000, GetColorU32(c::element::notify::blue_status));
                             };

                             edited::CheckboxWithPopup(true, "Silent Aimbot", "", &var::check[1], &var::key[1], &var::m[1], &var::value[1], true, true, var::col1, picker_flags | ImGuiColorEditFlags_NoAlpha);

                             edited::CheckboxWithPopup(false, "Automatic Fire", "", &var::check[2], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             if (edited::BeginPopupW("fire", "Setting up the 'Automatic fire' function."))
                             {
                                 edited::SliderInt("Speed Of Fire", &var::slider_value[0], 0, 30, "%d");

                                 edited::EndPopupW();
                             }

                             edited::CheckboxWithPopup(false, "Penetrate Walls", "-", &var::check[3], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             edited::SliderInt("Field Of View", &var::slider_value[1], -180, 180, "%d\xC2\xB0");

                         }
                         edited::EndChild();

                         edited::BeginChild("Selection", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::Combo("Target", &var::selected[0], var::items,  IM_ARRAYSIZE(var::items), 4);

                             edited::Combo("Hitboxes", &var::selected[1], var::items, IM_ARRAYSIZE(var::items), 4);

                             static float color[4] = { 1.f, 1.f, 1.f, 1.f };
                             edited::ColorEdit4("Just Picker", color, picker_flags);
                         }
                         edited::EndChild();

                         edited::BeginChild("Accuracy", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::SliderInt("Hit Chance", &var::slider_value[2], 0, 100, "%d%%");

                             if (edited::BeginPopupW("hit change", ""))
                             {
                                 edited::Keybind("Bind", &var::key[3], &var::m[3], &var::value[3]);
                                 edited::EndPopupW();
                             }

                             edited::SliderInt("Max. Misses", &var::slider_value[3], 0, 250, "%d");

                             if (edited::CheckboxWithPopup(false, "Static Point Scale", "", &var::check[4], NULL, NULL, NULL, NULL, NULL, NULL, NULL))
                             {
                                 notificationSystem.AddNotification("2", "Success!", "Your changes have been saved.", 2000, GetColorU32(c::element::notify::green_status));
                             };

                             if (edited::CheckboxWithPopup(false, "Head Safety If Lethal", "", &var::check[5], NULL, NULL, NULL, NULL, NULL, NULL, NULL))
                             {
                                 notificationSystem.AddNotification("4", "Warning!", "This action cannot be undone.", 2000, GetColorU32(c::element::notify::yellow_status));
                             };

                         }
                         edited::EndChild();
                     }
                     ImGui::EndGroup();

                     ImGui::SameLine();

                     ImGui::BeginGroup();
                     {
                         edited::BeginChild("Min. Damage", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::Combo("History", &var::selected[2], var::items, IM_ARRAYSIZE(var::items), 4);

                             if (edited::BeginPopupW("history ", "Setting up the 'history' function."))
                             {
                                 edited::CheckboxWithPopup(false, "Activity Team", "", &var::check[6], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                                 if (edited::BeginPopupW("fire", "Setting up the 'Automatic fire' function."))
                                 {

                                     TextColored(ImColor(GetColorU32(c::element::text_active)), "There is nothing yet.");

                                     TextColored(ImColor(GetColorU32(c::element::text_active)), "There is nothing yet.");

                                     TextColored(ImColor(GetColorU32(c::element::text_active)), "There is nothing yet.");

                                     edited::EndPopupW();
                                 }

                                 edited::CheckboxWithPopup(false, "Addons Manager", "", &var::check[7], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                                 edited::CheckboxWithPopup(false, "Visual Pressure", "", &var::check[8], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                                 edited::EndPopupW();
                             }

                             edited::CheckboxWithPopup(false, "Delay Shot", "", &var::check[9], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             edited::CheckboxWithPopup(false, "Duck Peek Assist", "", &var::check[10], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             edited::CheckboxWithPopup(true, "Speed Up Fire Rate", "", &var::check[11], &var::key[2], &var::m[2], &var::value[2], false, false, NULL, NULL);

                             edited::CheckboxWithPopup(true, "Magic Bullet", "", &var::check[12], &var::key[3], &var::m[3], &var::value[3], false, false, NULL, NULL);
                         }
                         edited::EndChild();

                         edited::BeginChild("Exploits", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {
                             edited::CheckboxWithPopup(true, "Enabled", "-", &var::check[13], &var::key[4], &var::m[4], &var::value[4], NULL, NULL, NULL, NULL);

                             edited::Combo("Pitch", &var::selected[3], var::items, IM_ARRAYSIZE(var::items), 4);
                         }
                         edited::EndChild();

                         edited::BeginChild("Others", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                         }
                         edited::EndChild();
                     }
                     ImGui::EndGroup();
                 }
                 else if (var::active_tab == 2)
                 {
                     ImGui::BeginGroup();
                     {
                         edited::BeginChild("Players", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::CheckboxWithPopup(true, "Enabled", "-", &var::check[13], NULL, NULL, NULL, false, true, var::col2, picker_flags);

                             edited::CheckboxWithPopup(false, "Teammates", "-", &var::check[14], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             if (edited::BeginPopupW("teammates", "Setting up the 'Teammates' function."))
                             {
                                 edited::Keybind("Bind", &var::key[2], &var::m[2], &var::value[2]);
                                 edited::EndPopupW();
                             }

                             edited::CheckboxWithPopup(false, "Behid Walls", "-", &var::check[15], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             edited::Combo("Bullet Tracer", &var::selected[5], var::items, IM_ARRAYSIZE(var::items), 4);

                             if (edited::BeginPopupW("bullet tracer", "Setting up the 'Bullet Tracer' function."))
                             {
                                 edited::ColorPicker4("Color", var::col13, picker_flags);
                                 edited::EndPopupW();
                             }

                             edited::Combo("Sound ESP", &var::selected[6], var::items, IM_ARRAYSIZE(var::items), 4);

                             if (edited::BeginPopupW("sound esp", "Setting up the 'Sound ESP' function."))
                             {
                                 edited::CheckboxWithPopup(false, "Steps", "-", &var::check[33], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                                 edited::CheckboxWithPopup(true, "Silent Footsteps", "The silent footsteps of the opponents will show his location, leaving circles gradually disappearing.", &var::check[34], NULL, NULL, NULL, false, false, NULL, NULL);

                                 edited::EndPopupW();
                             }

                             edited::Combo("Offscreen ESP", &var::selected[7], var::items, IM_ARRAYSIZE(var::items), 4);

                             edited::CheckboxWithPopup(true, "Dormant", "-", &var::check[17], NULL, NULL, NULL, false, true, var::col4, picker_flags);

                         }
                         edited::EndChild();

                         edited::BeginChild("Models", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::CheckboxWithPopup(false, "Emenies", "-", &var::check[20], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                             edited::CheckboxWithPopup(false, "Teammates", "-", &var::check[21], NULL, NULL, NULL, NULL, NULL, NULL, NULL);

                         }
                         edited::EndChild();
                     }
                     ImGui::EndGroup();

                     ImGui::SameLine();

                     ImGui::BeginGroup();
                     {
                         edited::BeginChild("World", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::CheckboxWithPopup(true, "Bomb", "-", &var::check[22], &var::key[6], &var::m[6], &var::value[6], true, false, var::col6, picker_flags);

                             edited::CheckboxWithPopup(true, "Weapons", "-", &var::check[23], &var::key[7], &var::m[7], &var::value[7], true, false, var::col7, picker_flags);

                             edited::CheckboxWithPopup(true, "Grenades", "-", &var::check[24], &var::key[8], &var::m[8], &var::value[8], true, false, var::col8, picker_flags);

                             edited::CheckboxWithPopup(true, "Grenade Trajectory", "-", &var::check[25], &var::key[9], &var::m[9], &var::value[9], true, false, var::col9, picker_flags);

                             edited::CheckboxWithPopup(true, "Grenade Proximity Warning", "-", &var::check[26], NULL, NULL, NULL, false, true, var::col10, picker_flags);

                         }
                         edited::EndChild();

                         edited::BeginChild("Common", ImVec2((region.x - (145 + spacing.x * 3)) / 2, 0));
                         {

                             edited::CheckboxWithPopup(true, "Night Mode", "-", &var::check[27], NULL, NULL, NULL, false, true, var::col11, picker_flags);

                             edited::Combo("Force Thridperson", &var::selected[4], var::items, IM_ARRAYSIZE(var::items), 4);

                         }
                         edited::EndChild();
                     }
                     ImGui::EndGroup();
                 }

                 ImGui::EndChild();
             }
             PopClipRect();
         }
         ImGui::End();

         SetNextWindowPos(ImVec2(20, 20));

         PushStyleColor(ImGuiCol_WindowBg, GetColorU32(c::element::info_bar::filling));
         PushStyleColor(ImGuiCol_Border, GetColorU32(c::element::info_bar::stroke));
         PushStyleVar(ImGuiStyleVar_WindowBorderSize, 1.f);
         PushStyleVar(ImGuiStyleVar_WindowRounding, c::element::rounding);
         PushStyleVar(ImGuiStyleVar_WindowPadding, ImVec2(10, 10));
         PushStyleVar(ImGuiStyleVar_ItemSpacing, ImVec2(10, 10));
         Begin("INFO_BAR", nullptr, ImGuiWindowFlags_NoDecoration | ImGuiWindowFlags_AlwaysAutoResize);
         {
             const ImVec2& pos = GetWindowPos();
             const ImVec2& region = GetContentRegionMax();
             const ImVec2& spacing = style->ItemSpacing;

             struct InfoBoxData {
                 const char* icon;
                 const char* label;
                 ImVec2 size;
             };

             std::vector<InfoBoxData> infoBoxes = { {"g", "SQUIDBOUNCE", ImVec2(150, 40)}, {"x", "[e]HvH", ImVec2(100, 40)}, {"d", "119FPS", ImVec2(100, 40)}, {"j", "49PING", ImVec2(100, 40)}, {"i", "12:15PM", ImVec2(100, 40)} };

             for (const auto& box : infoBoxes) {
                 edited::info_box(box.icon, box.label, box.size);

                 if (&box != &infoBoxes.back()) {
                     ImGui::SameLine();
                 }
             }
         }
         End();
         PopStyleVar(4);
         PopStyleColor(2);

         if (esp_preview::active_esp_preview) {
             PushStyleVar(ImGuiStyleVar_WindowRounding, 8.f);
             PushStyleVar(ImGuiStyleVar_WindowPadding, ImVec2(10, 10));
             PushStyleVar(ImGuiStyleVar_ItemSpacing, ImVec2(10, 10));

             PushStyleColor(ImGuiCol_WindowBg, GetColorU32(c::black, 0.7f));
             PushStyleColor(ImGuiCol_WindowShadow, GetColorU32(c::accent, 0.3f));
             PushStyleColor(ImGuiCol_Text, GetColorU32(c::element::text_active));

             const auto& window = ImGui::FindWindowByName("MENU");
             ImGui::SetNextWindowPos(window->Pos + ImVec2(window->Size.x + 25, 18));
             ImGui::Begin("esp preview", nullptr, ImGuiWindowFlags_AlwaysAutoResize | ImGuiWindowFlags_NoDecoration);
             {
                 edited::esp_preview(texture::preview,
                     &esp_preview::nickname, esp_preview::nick_color,
                     &esp_preview::weapon, esp_preview::weapon_color,
                     &esp_preview::hp, esp_preview::hp_color,
                     &esp_preview::zoom, esp_preview::zoom_color,
                     &esp_preview::bomb, esp_preview::bomb_color,
                     &esp_preview::c4, esp_preview::c4_color,
                     &esp_preview::money, esp_preview::money_color,
                     &esp_preview::hit, esp_preview::hit_color,
                     &esp_preview::box, esp_preview::box_color,
                     &esp_preview::HP_line, esp_preview::hp_line_color);
             }
             ImGui::End();

             PopStyleColor(3);
             PopStyleVar(3);
         }

     }
     ImGui::Render();
     const float clear_color_with_alpha[4] = { clear_color.x * clear_color.w, clear_color.y * clear_color.w, clear_color.z * clear_color.w, clear_color.w };
     g_pd3dDeviceContext->OMSetRenderTargets(1, &g_mainRenderTargetView, nullptr);
     g_pd3dDeviceContext->ClearRenderTargetView(g_mainRenderTargetView, clear_color_with_alpha);
     ImGui_ImplDX11_RenderDrawData(ImGui::GetDrawData());

     g_pSwapChain->Present(1, 0);
 }

 ImGui_ImplDX11_Shutdown();
 ImGui_ImplWin32_Shutdown();
 ImGui::DestroyContext();

 CleanupDeviceD3D();
 ::DestroyWindow(hwnd);
 ::UnregisterClassW(wc.lpszClassName, wc.hInstance);
