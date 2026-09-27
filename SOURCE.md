# NetVision — открытые исходники

Этот файл содержит текстовые исходники проекта для просмотра.


--- build.gradle.kts ---

import com.github.jengelman.gradle.plugins.shadow.transformers.ServiceFileTransformer
import net.minecrell.pluginyml.bukkit.BukkitPluginDescription.Permission

plugins {
    id("java")
    id("io.freefair.lombok") version "8.6"
    id("com.gradleup.shadow") version "9.0.0-beta6"
    id("net.minecrell.plugin-yml.bukkit") version "0.6.0"
    id("net.ltgt.errorprone") version "4.3.0"
    id("com.diffplug.spotless") version "7.2.1"
}

group = "club.nezxenka.netvision"
version = "1.0"

repositories {
    mavenCentral()
    maven("https://repo.papermc.io/repository/maven-public/")
    maven("https://repo.codemc.io/repository/maven-releases/")
    maven("https://repo.codemc.io/repository/maven-snapshots/")
    maven("https://maven.enginehub.org/repo/")
    maven("https://repo.extendedclip.com/content/repositories/placeholderapi/")
    maven("https://repo.opencollab.dev/maven-snapshots/")
    maven("https://jitpack.io")
}

dependencies {
    compileOnly("com.destroystokyo.paper:paper-api:1.16.5-R0.1-SNAPSHOT")
    compileOnly("com.sk89q.worldguard:worldguard-bukkit:7.0.9")
    compileOnly("me.clip:placeholderapi:2.11.6")
    compileOnly("com.github.decentsoftware-eu:decentholograms:2.8.17")
    implementation("com.github.retrooper:packetevents-spigot:2.13.0")
    implementation("org.incendo:cloud-paper:2.0.0-beta.16")
    implementation("org.incendo:cloud-processors-requirements:1.0.0-rc.1")
    implementation("net.kyori:adventure-platform-bukkit:4.4.1")
    implementation("net.kyori:adventure-text-minimessage:4.17.0")
    implementation("com.zaxxer:HikariCP:7.0.2")
    implementation("org.slf4j:slf4j-jdk14:2.0.17")
    compileOnly("org.geysermc.floodgate:api:2.0-SNAPSHOT")
    compileOnly("org.projectlombok:lombok:1.18.32")
    annotationProcessor("org.projectlombok:lombok:1.18.32")
    implementation("it.unimi.dsi:fastutil:8.5.15")
    implementation("org.jetbrains:annotations:24.1.0")
    implementation("com.google.flatbuffers:flatbuffers-java:25.2.10")
    implementation("com.google.code.gson:gson:2.10.1")
    implementation("com.fasterxml.jackson.core:jackson-databind:2.21.2")
    implementation("io.lettuce:lettuce-core:6.5.0.RELEASE") { exclude(group = "io.netty") }
    compileOnly("io.netty:netty-handler:4.1.113.Final")
    errorprone("com.google.errorprone:error_prone_core:2.41.0")
}

java {
    toolchain.languageVersion.set(JavaLanguageVersion.of(21))
}

tasks.withType<JavaCompile> {
    options.release.set(17)
    options.encoding = "UTF-8"
}

tasks.shadowJar {
    archiveBaseName.set(rootProject.name)
    archiveClassifier.set("")

    minimize {
        exclude(dependency("org.slf4j:slf4j-api"))
        exclude(dependency("org.slf4j:slf4j-jdk14"))
        exclude(dependency("net.kyori:adventure-text-serializer-gson"))
        exclude(dependency("net.kyori:adventure-text-serializer-json"))
        exclude(dependency("net.kyori:adventure-text-serializer-legacy"))
        exclude(dependency("io.lettuce:lettuce-core"))
        exclude(dependency("com.fasterxml.jackson.core:jackson-databind"))
        exclude(dependency("com.fasterxml.jackson.core:jackson-core"))
        exclude(dependency("com.fasterxml.jackson.core:jackson-annotations"))
        exclude(dependency("io.projectreactor:reactor-core"))
        exclude(dependency("org.reactivestreams:reactive-streams"))
    }

    transformers.add(ServiceFileTransformer())

    relocate("com.github.retrooper.packetevents", "club.nezxenka.netvision.libs.packetevents.api")
    relocate("io.github.retrooper.packetevents", "club.nezxenka.netvision.libs.packetevents.impl")
    relocate("net.kyori", "club.nezxenka.netvision.libs.kyori")
    relocate("com.google.gson", "club.nezxenka.netvision.libs.gson")
    relocate("org.incendo", "club.nezxenka.netvision.libs.incendo")
    relocate("io.leangen.geantyref", "club.nezxenka.netvision.libs.geantyref")
    relocate("it.unimi.dsi.fastutil", "club.nezxenka.netvision.libs.fastutil")
    relocate("com.google.flatbuffers", "club.nezxenka.netvision.libs.flatbuffers")
    relocate("com.zaxxer.hikari", "club.nezxenka.netvision.libs.hikari")
    relocate("org.slf4j", "club.nezxenka.netvision.libs.slf4j")
    relocate("org.jetbrains", "club.nezxenka.netvision.libs.jetbrains")
    relocate("org.intellij", "club.nezxenka.netvision.libs.intellij")
    relocate("com.fasterxml.jackson", "club.nezxenka.netvision.libs.jackson")
    relocate("io.lettuce", "club.nezxenka.netvision.libs.lettuce")
    relocate("reactor", "club.nezxenka.netvision.libs.reactor")
    relocate("org.reactivestreams", "club.nezxenka.netvision.libs.reactivestreams")
}

tasks.build {
    dependsOn(tasks.shadowJar)
}

tasks.withType<JavaCompile>().configureEach {
    dependsOn(tasks.spotlessApply)
}

bukkit {
    name = "NetVision"
    main = "club.nezxenka.netvision.NetVision"
    version = project.version.toString()
    apiVersion = "1.13"
    authors = listOf(
        "nezxenka"
    )
    softDepend = listOf(
        "ProtocolLib",
        "ProtocolSupport",
        "Essentials",
        "ViaVersion",
        "ViaBackwards",
        "ViaRewind",
        "Geyser-Spigot",
        "floodgate",
        "FastLogin",
        "PlaceholderAPI",
        "WorldGuard",
        "DecentHolograms",
    )

    permissions {
        register("netvision.*") {
            description = "Все права"
            default = Permission.Default.OP
            children = listOf(
                "netvision.help",
                "netvision.alerts",
                "netvision.alerts.enable-on-join",
                "netvision.menu",
                "netvision.status",
                "netvision.reload",
                "netvision.prob",
                "netvision.profile",
                "netvision.brand",
                "netvision.brand.enable-on-join",
                "netvision.falsepositive",
                "netvision.ban"
            )
        }
        register("netvision.help") {
            description = "Показывает хелп"
            default = Permission.Default.OP
        }
        register("netvision.alerts") {
            description = "Получение уведомлений о нарушениях"
            default = Permission.Default.OP
        }
        register("netvision.alerts.enable-on-join") {
            description = "Автоматически включает оповещения при присоединении"
            default = Permission.Default.OP
        }
        register("netvision.menu") {
            description = "Открывает курятник"
            default = Permission.Default.OP
        }
        register("netvision.status") {
            description = "Включает голограмму над игроками"
            default = Permission.Default.OP
        }
        register("netvision.reload") {
            description = "Перезагрузка конфига"
            default = Permission.Default.OP
        }
        register("netvision.exempt") {
            description = "Исключение для всех чеков"
            default = Permission.Default.FALSE
        }
        register("netvision.falsepositive") {
            description = "Сохранение тиков для анализа false positive"
            default = Permission.Default.OP
        }
        register("netvision.prob") {
            description = "Разрешает смотреть вероятность (пробу)"
            default = Permission.Default.OP
        }
        register("netvision.profile") {
            description = "Смотреть профиль игрока"
            default = Permission.Default.OP
        }
        register("netvision.brand") {
            description = "Получение уведомлений о версии"
            default = Permission.Default.OP
        }
        register("netvision.brand.enable-on-join") {
            description = "Автоматически включает уведомления о версии при входе"
            default = Permission.Default.OP
        }
        register("netvision.ban") {
            description = "Бан через /nvp ban"
            default = Permission.Default.OP
        }
    }
}

spotless {
    isEnforceCheck = true

    java {
        importOrder()

        removeUnusedImports()

        googleJavaFormat("1.17.0")
    }
}


--- gradlew ---

#!/bin/sh

#
# Copyright © 2015 the original authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# SPDX-License-Identifier: Apache-2.0
#

##############################################################################
#
#   Gradle start up script for POSIX generated by Gradle.
#
#   Important for running:
#
#   (1) You need a POSIX-compliant shell to run this script. If your /bin/sh is
#       noncompliant, but you have some other compliant shell such as ksh or
#       bash, then to run this script, type that shell name before the whole
#       command line, like:
#
#           ksh Gradle
#
#       Busybox and similar reduced shells will NOT work, because this script
#       requires all of these POSIX shell features:
#         * functions;
#         * expansions «$var», «${var}», «${var:-default}», «${var+SET}»,
#           «${var#prefix}», «${var%suffix}», and «$( cmd )»;
#         * compound commands having a testable exit status, especially «case»;
#         * various built-in commands including «command», «set», and «ulimit».
#
#   Important for patching:
#
#   (2) This script targets any POSIX shell, so it avoids extensions provided
#       by Bash, Ksh, etc; in particular arrays are avoided.
#
#       The "traditional" practice of packing multiple parameters into a
#       space-separated string is a well documented source of bugs and security
#       problems, so this is (mostly) avoided, by progressively accumulating
#       options in "$@", and eventually passing that to Java.
#
#       Where the inherited environment variables (DEFAULT_JVM_OPTS, JAVA_OPTS,
#       and GRADLE_OPTS) rely on word-splitting, this is performed explicitly;
#       see the in-line comments for details.
#
#       There are tweaks for specific operating systems such as AIX, CygWin,
#       Darwin, MinGW, and NonStop.
#
#   (3) This script is generated from the Groovy template
#       https://github.com/gradle/gradle/blob/HEAD/platforms/jvm/plugins-application/src/main/resources/org/gradle/api/internal/plugins/unixStartScript.txt
#       within the Gradle project.
#
#       You can find Gradle at https://github.com/gradle/gradle/.
#
##############################################################################

# Attempt to set APP_HOME

# Resolve links: $0 may be a link
app_path=$0

# Need this for daisy-chained symlinks.
while
    APP_HOME=${app_path%"${app_path##*/}"}  # leaves a trailing /; empty if no leading path
    [ -h "$app_path" ]
do
    ls=$( ls -ld "$app_path" )
    link=${ls#*' -> '}
    case $link in             #(
      /*)   app_path=$link ;; #(
      *)    app_path=$APP_HOME$link ;;
    esac
done

# This is normally unused
# shellcheck disable=SC2034
APP_BASE_NAME=${0##*/}
# Discard cd standard output in case $CDPATH is set (https://github.com/gradle/gradle/issues/25036)
APP_HOME=$( cd -P "${APP_HOME:-./}" > /dev/null && printf '%s\n' "$PWD" ) || exit

# Use the maximum available, or set MAX_FD != -1 to use that value.
MAX_FD=maximum

warn () {
    echo "$*"
} >&2

die () {
    echo
    echo "$*"
    echo
    exit 1
} >&2

# OS specific support (must be 'true' or 'false').
cygwin=false
msys=false
darwin=false
nonstop=false
case "$( uname )" in                #(
  CYGWIN* )         cygwin=true  ;; #(
  Darwin* )         darwin=true  ;; #(
  MSYS* | MINGW* )  msys=true    ;; #(
  NONSTOP* )        nonstop=true ;;
esac



# Determine the Java command to use to start the JVM.
if [ -n "$JAVA_HOME" ] ; then
    if [ -x "$JAVA_HOME/jre/sh/java" ] ; then
        # IBM's JDK on AIX uses strange locations for the executables
        JAVACMD=$JAVA_HOME/jre/sh/java
    else
        JAVACMD=$JAVA_HOME/bin/java
    fi
    if [ ! -x "$JAVACMD" ] ; then
        die "ERROR: JAVA_HOME is set to an invalid directory: $JAVA_HOME

Please set the JAVA_HOME variable in your environment to match the
location of your Java installation."
    fi
else
    JAVACMD=java
    if ! command -v java >/dev/null 2>&1
    then
        die "ERROR: JAVA_HOME is not set and no 'java' command could be found in your PATH.

Please set the JAVA_HOME variable in your environment to match the
location of your Java installation."
    fi
fi

# Increase the maximum file descriptors if we can.
if ! "$cygwin" && ! "$darwin" && ! "$nonstop" ; then
    case $MAX_FD in #(
      max*)
        # In POSIX sh, ulimit -H is undefined. That's why the result is checked to see if it worked.
        # shellcheck disable=SC2039,SC3045
        MAX_FD=$( ulimit -H -n ) ||
            warn "Could not query maximum file descriptor limit"
    esac
    case $MAX_FD in  #(
      '' | soft) :;; #(
      *)
        # In POSIX sh, ulimit -n is undefined. That's why the result is checked to see if it worked.
        # shellcheck disable=SC2039,SC3045
        ulimit -n "$MAX_FD" ||
            warn "Could not set maximum file descriptor limit to $MAX_FD"
    esac
fi

# Collect all arguments for the java command, stacking in reverse order:
#   * args from the command line
#   * the main class name
#   * -classpath
#   * -D...appname settings
#   * --module-path (only if needed)
#   * DEFAULT_JVM_OPTS, JAVA_OPTS, and GRADLE_OPTS environment variables.

# For Cygwin or MSYS, switch paths to Windows format before running java
if "$cygwin" || "$msys" ; then
    APP_HOME=$( cygpath --path --mixed "$APP_HOME" )

    JAVACMD=$( cygpath --unix "$JAVACMD" )

    # Now convert the arguments - kludge to limit ourselves to /bin/sh
    for arg do
        if
            case $arg in                                #(
              -*)   false ;;                            # don't mess with options #(
              /?*)  t=${arg#/} t=/${t%%/*}              # looks like a POSIX filepath
                    [ -e "$t" ] ;;                      #(
              *)    false ;;
            esac
        then
            arg=$( cygpath --path --ignore --mixed "$arg" )
        fi
        # Roll the args list around exactly as many times as the number of
        # args, so each arg winds up back in the position where it started, but
        # possibly modified.
        #
        # NB: a `for` loop captures its iteration list before it begins, so
        # changing the positional parameters here affects neither the number of
        # iterations, nor the values presented in `arg`.
        shift                   # remove old arg
        set -- "$@" "$arg"      # push replacement arg
    done
fi


# Add default JVM options here. You can also use JAVA_OPTS and GRADLE_OPTS to pass JVM options to this script.
DEFAULT_JVM_OPTS='"-Xmx64m" "-Xms64m"'

# Collect all arguments for the java command:
#   * DEFAULT_JVM_OPTS, JAVA_OPTS, and optsEnvironmentVar are not allowed to contain shell fragments,
#     and any embedded shellness will be escaped.
#   * For example: A user cannot expect ${Hostname} to be expanded, as it is an environment variable and will be
#     treated as '${Hostname}' itself on the command line.

set -- \
        "-Dorg.gradle.appname=$APP_BASE_NAME" \
        -jar "$APP_HOME/gradle/wrapper/gradle-wrapper.jar" \
        "$@"

# Stop when "xargs" is not available.
if ! command -v xargs >/dev/null 2>&1
then
    die "xargs is not available"
fi

# Use "xargs" to parse quoted args.
#
# With -n1 it outputs one arg per line, with the quotes and backslashes removed.
#
# In Bash we could simply go:
#
#   readarray ARGS < <( xargs -n1 <<<"$var" ) &&
#   set -- "${ARGS[@]}" "$@"
#
# but POSIX shell has neither arrays nor command substitution, so instead we
# post-process each arg (as a line of input to sed) to backslash-escape any
# character that might be a shell metacharacter, then use eval to reverse
# that process (while maintaining the separation between arguments), and wrap
# the whole thing up as a single "set" statement.
#
# This will of course break if any of these variables contains a newline or
# an unmatched quote.
#

eval "set -- $(
        printf '%s\n' "$DEFAULT_JVM_OPTS $JAVA_OPTS $GRADLE_OPTS" |
        xargs -n1 |
        sed ' s~[^-[:alnum:]+,./:=@_]~\\&~g; ' |
        tr '\n' ' '
    )" '"$@"'

exec "$JAVACMD" "$@"


--- LICENSE ---

MIT License

Copyright (c) 2026 Aleks Kuznetsov

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


--- settings.gradle.kts ---

pluginManagement {
    repositories {
        mavenCentral()
        gradlePluginPortal()
    }
}

rootProject.name = "NetVision"


--- src/main/java/club/nezxenka/netvision/actor/manager/lifecycle/PlayerLifecycleHandler.java ---

package club.nezxenka.netvision.actor.manager.lifecycle;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import org.bukkit.entity.Player;

public class PlayerLifecycleHandler {
  private final NetVision plugin;
  private final SignalManager alertManager;

  public PlayerLifecycleHandler(NetVision plugin, SignalManager alertManager) {
    this.plugin = plugin;
    this.alertManager = alertManager;
  }

  public void onJoinAutoEnable(Player player) {
    if (player.hasPermission("netvision.alerts")
        && player.hasPermission("netvision.alerts.enable-on-join")) {
      if (!alertManager.hasAlertsEnabled(player, SignalType.REGULAR))
        alertManager.toggle(player, SignalType.REGULAR, true);
    }
    if (player.hasPermission("netvision.brand")
        && player.hasPermission("netvision.brand.enable-on-join")) {
      if (!alertManager.hasAlertsEnabled(player, SignalType.BRAND))
        alertManager.toggle(player, SignalType.BRAND, true);
    }
  }

  public void onQuit(Player player) {
    plugin.getChickenCoopMenu().removePlayer(player.getUniqueId());
    plugin.getHologramManager().handlePlayerQuit(player);
    alertManager.handlePlayerQuit(player);
  }
}


--- src/main/java/club/nezxenka/netvision/actor/manager/PlayerDataManager.java ---

package club.nezxenka.netvision.actor.manager;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.integration.geyser.GeyserUtil;
import club.nezxenka.netvision.integration.worldguard.WorldGuardManager;
import club.nezxenka.netvision.remote.provider.AIServerProvider;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import java.util.Collection;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.player.PlayerJoinEvent;
import org.bukkit.event.player.PlayerQuitEvent;

public class PlayerDataManager implements Listener {
  private final NetVision plugin;
  private final SignalManager alertManager;
  private final ConfigManager configManager;
  private final DatabaseManager databaseManager;
  private final WorldGuardManager worldGuardManager;
  private AIServerProvider aiServerProvider;
  private final Map<UUID, NetVisionPlayer> players = new ConcurrentHashMap<>();

  public PlayerDataManager(
      NetVision plugin,
      SignalManager alertManager,
      ConfigManager configManager,
      DatabaseManager databaseManager,
      AIServerProvider aiServerProvider,
      WorldGuardManager worldGuardManager) {
    this.plugin = plugin;
    this.alertManager = alertManager;
    this.configManager = configManager;
    this.databaseManager = databaseManager;
    this.aiServerProvider = aiServerProvider;
    this.worldGuardManager = worldGuardManager;
    plugin.getServer().getPluginManager().registerEvents(this, plugin);
  }

  @EventHandler
  public void onJoin(PlayerJoinEvent event) {
    Player player = event.getPlayer();
    if (player.hasPermission("netvision.alerts")
        && player.hasPermission("netvision.alerts.enable-on-join")) {
      if (!alertManager.hasAlertsEnabled(player, SignalType.REGULAR))
        alertManager.toggle(player, SignalType.REGULAR, true);
    }
    if (player.hasPermission("netvision.brand")
        && player.hasPermission("netvision.brand.enable-on-join")) {
      if (!alertManager.hasAlertsEnabled(player, SignalType.BRAND))
        alertManager.toggle(player, SignalType.BRAND, true);
    }
    if (player.hasPermission("netvision.exempt")) return;
    NetVisionPlayer nvPlayer =
        new NetVisionPlayer(
            player,
            plugin,
            configManager,
            databaseManager,
            alertManager,
            aiServerProvider,
            worldGuardManager);
    nvPlayer.setBedrock(GeyserUtil.isBedrockPlayer(player.getUniqueId()));
    if (nvPlayer.isBedrockExempt())
      plugin
          .getLogger()
          .info("[Geyser] " + player.getName() + " is a Bedrock player, checks exempted.");
    players.put(player.getUniqueId(), nvPlayer);
    plugin.getChickenCoopMenu().restorePlayer(player.getUniqueId(), player.getName());
  }

  @EventHandler
  public void onQuit(PlayerQuitEvent event) {
    Player player = event.getPlayer();
    UUID uuid = event.getPlayer().getUniqueId();
    plugin.getChickenCoopMenu().removePlayer(player.getUniqueId());
    plugin.getHologramManager().handlePlayerQuit(player);
    alertManager.handlePlayerQuit(player);
    players.remove(uuid);
  }

  public NetVisionPlayer getPlayer(Player player) {
    return player == null ? null : players.get(player.getUniqueId());
  }

  public NetVisionPlayer getPlayer(UUID uuid) {
    return players.get(uuid);
  }

  public Collection<NetVisionPlayer> getPlayers() {
    return players.values();
  }

  public void reloadAllPlayers() {
    for (NetVisionPlayer nvPlayer : players.values()) nvPlayer.reload();
  }
}


--- src/main/java/club/nezxenka/netvision/actor/manager/registry/PlayerRegistry.java ---

package club.nezxenka.netvision.actor.manager.registry;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import java.util.Collection;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class PlayerRegistry {
  private final Map<UUID, NetVisionPlayer> players = new ConcurrentHashMap<>();

  public void register(UUID uuid, NetVisionPlayer player) {
    players.put(uuid, player);
  }

  public void unregister(UUID uuid) {
    players.remove(uuid);
  }

  public NetVisionPlayer get(UUID uuid) {
    return players.get(uuid);
  }

  public Collection<NetVisionPlayer> all() {
    return players.values();
  }

  public int count() {
    return players.size();
  }
}


--- src/main/java/club/nezxenka/netvision/actor/model/attribute/PlayerAttributeSet.java ---

package club.nezxenka.netvision.actor.model.attribute;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class PlayerAttributeSet {
  private double x;
  private double y;
  private double z;
  private float yaw;
  private float pitch;
  private float lastYaw;
  private float lastPitch;
  private String brand;
  private boolean bedrock;
}


--- src/main/java/club/nezxenka/netvision/actor/model/NetVisionPlayer.java ---

package club.nezxenka.netvision.actor.model;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.state.PlayerRotationData;
import club.nezxenka.netvision.actor.state.PlayerTeleportData;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.engine.coordinator.ModuleCoordinator;
import club.nezxenka.netvision.entity.compensation.CompensatedEntities;
import club.nezxenka.netvision.integration.worldguard.WorldGuardManager;
import club.nezxenka.netvision.remote.provider.AIServerProvider;
import club.nezxenka.netvision.service.enforce.internal.EnforcementManager;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.util.collection.Pair;
import club.nezxenka.netvision.util.latency.ILatencyUtils;
import club.nezxenka.netvision.util.latency.LatencyUtils;
import club.nezxenka.netvision.util.rotation.HeadRotation;
import club.nezxenka.netvision.util.rotation.PacketStateData;
import club.nezxenka.netvision.util.rotation.RotationUpdate;
import com.github.retrooper.packetevents.PacketEvents;
import com.github.retrooper.packetevents.protocol.player.ClientVersion;
import com.github.retrooper.packetevents.protocol.player.GameMode;
import com.github.retrooper.packetevents.protocol.player.User;
import it.unimi.dsi.fastutil.ints.IntArraySet;
import java.util.Queue;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicInteger;
import lombok.Getter;
import lombok.Setter;
import net.kyori.adventure.text.Component;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;

@Getter
public class NetVisionPlayer {
  private final UUID uuid;
  private final Player player;
  private final User user;
  private final ModuleCoordinator moduleCoordinator;
  private final EnforcementManager enforcementManager;
  public final CompensatedEntities compensatedEntities;
  public final ILatencyUtils latencyUtils;
  public final PacketStateData packetStateData = new PacketStateData();
  public final RotationUpdate rotationUpdate =
      new RotationUpdate(new HeadRotation(), new HeadRotation(), 0, 0);
  public final long joinTime;
  @Setter private int entityId;
  @Setter private GameMode gameMode = GameMode.SURVIVAL;
  @Setter private String brand = "vanilla";
  @Setter private boolean bedrock = false;

  public boolean isBedrockExempt() {
    return plugin.getConfigManager().isBedrockExemptEnabled() && bedrock;
  }

  public double x, y, z;
  public float yaw, pitch;
  public float lastYaw, lastPitch;

  private final Queue<PlayerTeleportData> pendingTeleports = new ConcurrentLinkedQueue<>();
  private final Queue<PlayerRotationData> pendingRotations = new ConcurrentLinkedQueue<>();

  @Setter private double dmgMultiplier = 1.0;
  public int ticksSinceAttack;

  public final Queue<Pair<Short, Long>> transactionsSent = new ConcurrentLinkedQueue<>();
  public final IntArraySet entitiesDespawnedThisTransaction = new IntArraySet();
  public final Set<Short> didWeSendThatTrans = ConcurrentHashMap.newKeySet();
  public final AtomicInteger lastTransactionSent = new AtomicInteger(0);
  public final AtomicInteger lastTransactionReceived = new AtomicInteger(0);
  private final AtomicInteger transactionIDCounter = new AtomicInteger(0);
  private final NetVision plugin;

  public NetVisionPlayer(
      Player player,
      NetVision plugin,
      ConfigManager configManager,
      DatabaseManager databaseManager,
      SignalManager alertManager,
      AIServerProvider aiServerProvider,
      WorldGuardManager worldGuardManager) {
    this.plugin = plugin;
    this.player = player;
    this.uuid = player.getUniqueId();
    this.user = PacketEvents.getAPI().getPlayerManager().getUser(player);
    this.joinTime = System.currentTimeMillis();
    this.latencyUtils = new LatencyUtils(this, plugin);
    this.compensatedEntities = new CompensatedEntities(this);
    this.moduleCoordinator =
        new ModuleCoordinator(
            this, plugin, configManager, aiServerProvider, worldGuardManager, alertManager);
    this.enforcementManager =
        new EnforcementManager(
            this, plugin, configManager, databaseManager.getDatabase(), alertManager);
    int sequence = configManager.getAiSequence();
    this.ticksSinceAttack = sequence + 1;
  }

  public boolean isPointThree() {
    return getUser().getClientVersion().isOlderThan(ClientVersion.V_1_18_2);
  }

  public double getMovementThreshold() {
    return isPointThree() ? 0.03 : 0.0002;
  }

  public boolean isCancelDuplicatePacket() {
    return true;
  }

  public void sendTransaction() {
    if (user.getConnectionState()
        != com.github.retrooper.packetevents.protocol.ConnectionState.PLAY) return;
    short transactionID = (short) (-1 * (transactionIDCounter.getAndIncrement() & 0x7FFF));
    didWeSendThatTrans.add(transactionID);
    com.github.retrooper.packetevents.wrapper.PacketWrapper<?> packet;
    if (PacketEvents.getAPI()
        .getServerManager()
        .getVersion()
        .isNewerThanOrEquals(
            com.github.retrooper.packetevents.manager.server.ServerVersion.V_1_17)) {
      packet =
          new com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerPing(
              transactionID);
    } else {
      packet =
          new com.github.retrooper.packetevents.wrapper.play.server
              .WrapperPlayServerWindowConfirmation((byte) 0, transactionID, false);
    }
    user.sendPacket(packet);
  }

  public void disconnect(Component reason) {
    String textReason =
        net.kyori.adventure.text.serializer.legacy.LegacyComponentSerializer.legacySection()
            .serialize(reason);
    user.sendPacket(
        new com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerDisconnect(
            reason));
    user.closeConnection();
    if (Bukkit.isPrimaryThread()) player.kickPlayer(textReason);
    else Bukkit.getScheduler().runTask(plugin, () -> player.kickPlayer(textReason));
  }

  public void reload() {
    if (this.enforcementManager != null) this.enforcementManager.reload();
    if (this.moduleCoordinator != null) this.moduleCoordinator.reloadModules();
  }
}


--- src/main/java/club/nezxenka/netvision/actor/model/network/NetworkProfile.java ---

package club.nezxenka.netvision.actor.model.network;

import com.github.retrooper.packetevents.protocol.player.ClientVersion;
import com.github.retrooper.packetevents.protocol.player.GameMode;
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class NetworkProfile {
  private ClientVersion clientVersion;
  private GameMode gameMode;
  private int entityId;
  private long joinTime;
}


--- src/main/java/club/nezxenka/netvision/actor/permission/cache/PermissionCache.java ---

package club.nezxenka.netvision.actor.permission.cache;

import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class PermissionCache {
  private final Map<UUID, Boolean> exemptCache = new ConcurrentHashMap<>();
  private final Map<UUID, Boolean> alertAutoEnableCache = new ConcurrentHashMap<>();
  private final Map<UUID, Boolean> brandAutoEnableCache = new ConcurrentHashMap<>();

  public void setExempt(UUID uuid, boolean value) {
    exemptCache.put(uuid, value);
  }

  public boolean isExempt(UUID uuid) {
    return exemptCache.getOrDefault(uuid, false);
  }

  public void setAlertAuto(UUID uuid, boolean value) {
    alertAutoEnableCache.put(uuid, value);
  }

  public boolean isAlertAuto(UUID uuid) {
    return alertAutoEnableCache.getOrDefault(uuid, false);
  }

  public void setBrandAuto(UUID uuid, boolean value) {
    brandAutoEnableCache.put(uuid, value);
  }

  public boolean isBrandAuto(UUID uuid) {
    return brandAutoEnableCache.getOrDefault(uuid, false);
  }

  public void clear(UUID uuid) {
    exemptCache.remove(uuid);
    alertAutoEnableCache.remove(uuid);
    brandAutoEnableCache.remove(uuid);
  }
}


--- src/main/java/club/nezxenka/netvision/actor/permission/PlayerPermissionResolver.java ---

package club.nezxenka.netvision.actor.permission;

import org.bukkit.entity.Player;

public class PlayerPermissionResolver {
  public static boolean hasAlertAutoEnable(Player player) {
    return player.hasPermission("netvision.alerts")
        && player.hasPermission("netvision.alerts.enable-on-join");
  }

  public static boolean hasBrandAutoEnable(Player player) {
    return player.hasPermission("netvision.brand")
        && player.hasPermission("netvision.brand.enable-on-join");
  }

  public static boolean isExempt(Player player) {
    return player.hasPermission("netvision.exempt");
  }
}


--- src/main/java/club/nezxenka/netvision/actor/state/PlayerRotationData.java ---

package club.nezxenka.netvision.actor.state;

import lombok.Getter;
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
@Getter
public class PlayerRotationData {
  private final float yaw;
  private final float pitch;
  private final int transactionId;
}


--- src/main/java/club/nezxenka/netvision/actor/state/PlayerTeleportData.java ---

package club.nezxenka.netvision.actor.state;

import com.github.retrooper.packetevents.protocol.teleport.RelativeFlag;
import com.github.retrooper.packetevents.util.Vector3d;
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
@Getter
public class PlayerTeleportData {
  private final Vector3d location;
  private final RelativeFlag flags;
  private final int transactionId;

  public boolean isRelativeX() {
    return flags.has(RelativeFlag.X);
  }

  public boolean isRelativeY() {
    return flags.has(RelativeFlag.Y);
  }

  public boolean isRelativeZ() {
    return flags.has(RelativeFlag.Z);
  }
}


--- src/main/java/club/nezxenka/netvision/actor/state/snapshot/PlayerStateSnapshot.java ---

package club.nezxenka.netvision.actor.state.snapshot;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class PlayerStateSnapshot {
  private double posX;
  private double posY;
  private double posZ;
  private float yaw;
  private float pitch;
  private boolean onGround;
  private long timestamp;
}


--- src/main/java/club/nezxenka/netvision/actor/state/TransactionStateTracker.java ---

package club.nezxenka.netvision.actor.state;

import club.nezxenka.netvision.util.collection.Pair;
import it.unimi.dsi.fastutil.ints.IntArraySet;
import java.util.Queue;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicInteger;

public class TransactionStateTracker {
  public final Queue<Pair<Short, Long>> transactionsSent = new ConcurrentLinkedQueue<>();
  public final IntArraySet entitiesDespawnedThisTransaction = new IntArraySet();
  public final Set<Short> didWeSendThatTrans = ConcurrentHashMap.newKeySet();
  public final AtomicInteger lastTransactionSent = new AtomicInteger(0);
  public final AtomicInteger lastTransactionReceived = new AtomicInteger(0);
  private final AtomicInteger transactionIDCounter = new AtomicInteger(0);

  public short nextTransactionId() {
    return (short) (-1 * (transactionIDCounter.getAndIncrement() & 0x7FFF));
  }
}


--- src/main/java/club/nezxenka/netvision/audience/api/factory/SenderProvider.java ---

package club.nezxenka.netvision.audience.api.factory;

import club.nezxenka.netvision.audience.api.Sender;

public interface SenderProvider {
  Sender create(org.bukkit.command.CommandSender base);
}


--- src/main/java/club/nezxenka/netvision/audience/api/mapper/SenderMapperAdapter.java ---

package club.nezxenka.netvision.audience.api.mapper;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.audience.factory.SenderFactory;
import org.bukkit.command.CommandSender;

public class SenderMapperAdapter {
  private final SenderFactory factory;

  public SenderMapperAdapter(NetVision plugin) {
    this.factory = new SenderFactory(plugin);
  }

  public Sender map(CommandSender base) {
    return factory.map(base);
  }

  public CommandSender reverse(Sender mapped) {
    return factory.reverse(mapped);
  }
}


--- src/main/java/club/nezxenka/netvision/audience/api/Sender.java ---

package club.nezxenka.netvision.audience.api;

import java.util.UUID;
import net.kyori.adventure.text.Component;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;

public interface Sender {
  UUID CONSOLE_UUID = new UUID(0, 0);
  String CONSOLE_NAME = "Console";

  String getName();

  UUID getUniqueId();

  void sendMessage(String message);

  void sendMessage(Component message);

  boolean hasPermission(String permission);

  boolean isConsole();

  boolean isPlayer();

  CommandSender getNativeSender();

  Player getPlayer();
}


--- src/main/java/club/nezxenka/netvision/audience/factory/SenderFactory.java ---

package club.nezxenka.netvision.audience.factory;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.audience.api.Sender;
import java.util.UUID;
import net.kyori.adventure.text.Component;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.incendo.cloud.SenderMapper;
import org.jetbrains.annotations.NotNull;

public class SenderFactory implements SenderMapper<CommandSender, Sender> {

  private final NetVision plugin;

  public SenderFactory(NetVision plugin) {
    this.plugin = plugin;
  }

  @Override
  public Sender map(@NotNull CommandSender base) {
    if (base instanceof Player player) return new PlayerSender(player, plugin);
    return new ConsoleSender(base, plugin);
  }

  @Override
  public CommandSender reverse(@NotNull Sender mapped) {
    return mapped.getNativeSender();
  }

  private static class PlayerSender implements Sender {

    private final Player player;
    private final NetVision plugin;

    PlayerSender(Player player, NetVision plugin) {
      this.player = player;
      this.plugin = plugin;
    }

    @Override
    public String getName() {
      return player.getName();
    }

    @Override
    public UUID getUniqueId() {
      return player.getUniqueId();
    }

    @Override
    public void sendMessage(String message) {
      player.sendMessage(message);
    }

    @Override
    public void sendMessage(Component message) {
      plugin.getAdventure().player(player).sendMessage(message);
    }

    @Override
    public boolean hasPermission(String permission) {
      return player.hasPermission(permission);
    }

    @Override
    public boolean isConsole() {
      return false;
    }

    @Override
    public boolean isPlayer() {
      return true;
    }

    @Override
    public CommandSender getNativeSender() {
      return player;
    }

    @Override
    public Player getPlayer() {
      return player;
    }
  }

  private static class ConsoleSender implements Sender {

    private final CommandSender sender;
    private final NetVision plugin;

    ConsoleSender(CommandSender sender, NetVision plugin) {
      this.sender = sender;
      this.plugin = plugin;
    }

    @Override
    public String getName() {
      return CONSOLE_NAME;
    }

    @Override
    public UUID getUniqueId() {
      return CONSOLE_UUID;
    }

    @Override
    public void sendMessage(String message) {
      sender.sendMessage(message);
    }

    @Override
    public void sendMessage(Component message) {
      plugin.getAdventure().sender(sender).sendMessage(message);
    }

    @Override
    public boolean hasPermission(String permission) {
      return sender.hasPermission(permission);
    }

    @Override
    public boolean isConsole() {
      return true;
    }

    @Override
    public boolean isPlayer() {
      return false;
    }

    @Override
    public CommandSender getNativeSender() {
      return sender;
    }

    @Override
    public Player getPlayer() {
      return null;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/core/config/core/ConfigManager.java ---

package club.nezxenka.netvision.core.config;

import club.nezxenka.netvision.NetVision;
import java.io.File;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.regex.Pattern;
import java.util.regex.PatternSyntaxException;
import java.util.stream.Collectors;
import lombok.Getter;
import lombok.Setter;
import org.bukkit.configuration.file.FileConfiguration;
import org.bukkit.configuration.file.YamlConfiguration;

@Getter
public class ConfigManager {

  private final NetVision plugin;
  private FileConfiguration config;
  private FileConfiguration punishments;
  private boolean aiEnabled;
  private String aiServerUrl;
  private String aiApiKey;

  @Setter private int aiSequence;

  private int aiStep;
  private double aiFlag;
  private double aiResetOnFlag;
  private double aiBufferMultiplier;
  private double aiBufferDecrease;
  private boolean aiDamageReductionEnabled;
  private double aiDamageReductionProb;
  private double aiDamageReductionMultiplier;
  private boolean aiWorldGuardEnabled;
  private List<String> aiDisabledRegions;
  private boolean aiCollectModeEnabled;
  private String aiCollectModeLabel;
  private List<Pattern> ignoredClientPatterns;
  private boolean disconnectBlacklistedForge;
  private double suspiciousAlertsBuffer;
  private List<String> enabledDebugCategories;
  private boolean bedrockExemptEnabled;

  public ConfigManager(NetVision plugin) {
    this.plugin = plugin;
    loadConfigs();
  }

  public void reloadConfig() {
    loadConfigs();
  }

  private void loadConfigs() {
    plugin.saveDefaultConfig();
    plugin.reloadConfig();
    this.config = plugin.getConfig();
    File punishmentsFile = new File(plugin.getDataFolder(), "punishments.yml");
    if (!punishmentsFile.exists()) {
      plugin.saveResource("punishments.yml", false);
    }
    this.punishments = YamlConfiguration.loadConfiguration(punishmentsFile);
    loadValues();
  }

  private void loadValues() {
    aiEnabled = config.getBoolean("ai.enabled", false);
    aiServerUrl = config.getString("ai.server", "");
    aiApiKey = config.getString("ai.api-key", "API-KEY");
    aiSequence = config.getInt("ai.sequence", 40);
    aiStep = config.getInt("ai.step", 10);
    aiFlag = config.getDouble("ai.buffer.flag", 50.0);
    aiResetOnFlag = config.getDouble("ai.buffer.reset-on-flag", 25.0);
    aiBufferMultiplier = config.getDouble("ai.buffer.multiplier", 100.0);
    aiBufferDecrease = config.getDouble("ai.buffer.decrease", 0.25);
    aiDamageReductionEnabled = config.getBoolean("ai.damage-reduction.enabled", true);
    aiDamageReductionProb = config.getDouble("ai.damage-reduction.prob", 0.9);
    aiDamageReductionMultiplier = config.getDouble("ai.damage-reduction.multiplier", 1.0);
    aiWorldGuardEnabled = config.getBoolean("ai.worldguard.enabled", true);
    aiDisabledRegions =
        config.getStringList("ai.worldguard.disabled-regions").stream()
            .map(String::toLowerCase)
            .collect(Collectors.toList());
    aiCollectModeEnabled = config.getBoolean("ai.collect-mode.enabled", false);
    aiCollectModeLabel = config.getString("ai.collect-mode.label", "legit");
    ignoredClientPatterns = new ArrayList<>();
    for (String pattern : config.getStringList("client-brand.ignored-clients")) {
      try {
        ignoredClientPatterns.add(Pattern.compile(pattern));
      } catch (PatternSyntaxException e) {
        plugin.getLogger().warning("[BrandScanner] Invalid regex pattern in config: " + pattern);
      }
    }
    disconnectBlacklistedForge =
        config.getBoolean("client-brand.disconnect-blacklisted-forge-versions", true);
    suspiciousAlertsBuffer = config.getDouble("suspicious.alerts.buffer", 25.0);
    enabledDebugCategories = config.getStringList("debug.enabled-categories");
    if (enabledDebugCategories == null) {
      enabledDebugCategories = Collections.emptyList();
    }
    bedrockExemptEnabled = config.getBoolean("exemptions.bedrock", true);
  }

  public boolean isBedrockExemptEnabled() {
    return bedrockExemptEnabled;
  }

  public boolean isClientIgnored(String brand) {
    for (Pattern pattern : ignoredClientPatterns) {
      if (pattern.matcher(brand).find()) {
        return true;
      }
    }
    return false;
  }
}


--- src/main/java/club/nezxenka/netvision/core/config/core/loader/ConfigFileLoader.java ---

package club.nezxenka.netvision.core.config.loader;

import java.io.File;
import org.bukkit.configuration.file.FileConfiguration;
import org.bukkit.configuration.file.YamlConfiguration;
import org.bukkit.plugin.java.JavaPlugin;

public class ConfigFileLoader {
  private final JavaPlugin plugin;

  public ConfigFileLoader(JavaPlugin plugin) {
    this.plugin = plugin;
  }

  public FileConfiguration loadOrCreate(String resourceName, String fileName) {
    plugin.saveDefaultConfig();
    plugin.reloadConfig();
    File file = new File(plugin.getDataFolder(), fileName);
    if (!file.exists()) plugin.saveResource(resourceName, false);
    return YamlConfiguration.loadConfiguration(file);
  }
}


--- src/main/java/club/nezxenka/netvision/core/config/core/section/AiConfigSection.java ---

package club.nezxenka.netvision.core.config.section;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class AiConfigSection {
  private boolean enabled;
  private String serverUrl;
  private String apiKey;
  private int sequence;
  private int step;
  private double flag;
  private double resetOnFlag;
  private double bufferMultiplier;
  private double bufferDecrease;
}


--- src/main/java/club/nezxenka/netvision/core/config/core/validator/ConfigValidator.java ---

package club.nezxenka.netvision.core.config.validator;

import java.util.logging.Logger;

public class ConfigValidator {
  private final Logger logger;

  public ConfigValidator(Logger logger) {
    this.logger = logger;
  }

  public boolean validateAiConfig(String url, String apiKey) {
    if (url == null || url.isEmpty()) {
      logger.warning("AI server URL is empty");
      return false;
    }
    if (apiKey == null || apiKey.equals("API-KEY")) {
      logger.warning("AI API key is not configured");
      return false;
    }
    return true;
  }

  public int clampPoolSize(int requested) {
    return Math.max(2, Math.min(8, requested));
  }
}


--- src/main/java/club/nezxenka/netvision/core/config/locale/loader/LocaleFileLoader.java ---

package club.nezxenka.netvision.core.locale.loader;

import java.io.File;
import org.bukkit.plugin.java.JavaPlugin;

public class LocaleFileLoader {
  private final JavaPlugin plugin;

  public LocaleFileLoader(JavaPlugin plugin) {
    this.plugin = plugin;
  }

  public File resolveFile(String locale, File messagesDir) {
    File file = new File(messagesDir, "messages_" + locale + ".yml");
    if (!file.exists()) return new File(messagesDir, "messages_en.yml");
    return file;
  }

  public void ensureDirectory(File dir) {
    if (!dir.exists()) dir.mkdirs();
  }

  public void saveIfMissing(String resourcePath) {
    File file = new File(plugin.getDataFolder(), resourcePath);
    if (!file.exists()) plugin.saveResource(resourcePath, false);
  }
}


--- src/main/java/club/nezxenka/netvision/core/config/locale/LocaleManager.java ---

package club.nezxenka.netvision.core.locale;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.util.message.Message;
import java.io.File;
import java.util.List;
import org.bukkit.configuration.file.FileConfiguration;
import org.bukkit.configuration.file.YamlConfiguration;

public class LocaleManager {

  private final NetVision plugin;
  private final ConfigManager configManager;
  private FileConfiguration messagesConfig;

  public LocaleManager(NetVision plugin, ConfigManager configManager) {
    this.plugin = plugin;
    this.configManager = configManager;
    reload();
  }

  public void reload() {
    String locale = configManager.getConfig().getString("locale", "en");
    File messagesDir = new File(plugin.getDataFolder(), "messages");
    if (!messagesDir.exists()) {
      messagesDir.mkdirs();
    }
    saveDefaultLocale("en");
    if (!locale.equalsIgnoreCase("en")) {
      saveDefaultLocale(locale);
    }
    File messagesFile = new File(messagesDir, "messages_" + locale + ".yml");
    if (!messagesFile.exists()) {
      plugin.getLogger().warning("Locale " + locale + " not found.");
      messagesFile = new File(messagesDir, "messages_en.yml");
    }
    this.messagesConfig = YamlConfiguration.loadConfiguration(messagesFile);
    File defaultFile = new File(messagesDir, "messages_en.yml");
    if (defaultFile.exists()) {
      this.messagesConfig.setDefaults(YamlConfiguration.loadConfiguration(defaultFile));
    }
  }

  private void saveDefaultLocale(String locale) {
    File dir = new File(plugin.getDataFolder(), "messages");
    File file = new File(dir, "messages_" + locale + ".yml");
    if (!file.exists()) {
      plugin.saveResource("messages/messages_" + locale + ".yml", false);
    }
  }

  public String getRawMessage(Message key) {
    return messagesConfig.getString(key.getPath(), "Missing message: " + key.getPath());
  }

  public List<String> getRawMessageList(Message key) {
    return messagesConfig.getStringList(key.getPath());
  }
}


--- src/main/java/club/nezxenka/netvision/core/config/locale/resolver/MessageKeyResolver.java ---

package club.nezxenka.netvision.core.locale.resolver;

import club.nezxenka.netvision.util.message.Message;
import java.util.List;
import org.bukkit.configuration.file.FileConfiguration;

public class MessageKeyResolver {
  private final FileConfiguration config;

  public MessageKeyResolver(FileConfiguration config) {
    this.config = config;
  }

  public String resolve(Message key) {
    return config.getString(key.getPath(), "Missing: " + key.getPath());
  }

  public List<String> resolveList(Message key) {
    return config.getStringList(key.getPath());
  }
}


--- src/main/java/club/nezxenka/netvision/core/diagnostic/internal/DebugManager.java ---

package club.nezxenka.netvision.core.diagnostic.internal;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.diagnostic.model.DebugCategory;
import java.util.EnumSet;
import java.util.List;
import java.util.Locale;
import java.util.Set;

public class DebugManager {

  private final NetVision plugin;
  private final ConfigManager configManager;
  private final Set<DebugCategory> enabledCategories = EnumSet.noneOf(DebugCategory.class);

  public DebugManager(NetVision plugin, ConfigManager configManager) {
    this.plugin = plugin;
    this.configManager = configManager;
    reload();
  }

  public void reload() {
    enabledCategories.clear();
    List<String> enabledKeys = configManager.getEnabledDebugCategories();
    for (String key : enabledKeys) {
      try {
        enabledCategories.add(DebugCategory.valueOf(key.toUpperCase(Locale.ROOT)));
      } catch (IllegalArgumentException e) {
        plugin.getLogger().warning("Invalid debug category in config: " + key);
      }
    }
  }

  public boolean isEnabled(DebugCategory category) {
    return enabledCategories.contains(category);
  }

  public void log(DebugCategory category, String message) {
    if (isEnabled(category)) {
      plugin.getLogger().info("[DEBUG | " + category.name() + "] " + message);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/core/diagnostic/internal/logger/DebugLogger.java ---

package club.nezxenka.netvision.core.diagnostic.internal.logger;

import club.nezxenka.netvision.core.diagnostic.model.DebugCategory;
import java.util.logging.Logger;

public class DebugLogger {
  private final Logger bukkitLogger;

  public DebugLogger(Logger bukkitLogger) {
    this.bukkitLogger = bukkitLogger;
  }

  public void log(DebugCategory category, String message, boolean enabled) {
    if (enabled) bukkitLogger.info("[DEBUG | " + category.name() + "] " + message);
  }
}


--- src/main/java/club/nezxenka/netvision/core/diagnostic/model/DebugCategory.java ---

package club.nezxenka.netvision.core.diagnostic.model;

public enum DebugCategory {
  AI_PROBABILITY,
  AI_TIMEOUT,
  WORLDGUARD,
  PACKET_DUPLICATION;
}


--- src/main/java/club/nezxenka/netvision/core/diagnostic/model/flag/DebugFlag.java ---

package club.nezxenka.netvision.core.diagnostic.model.flag;

import club.nezxenka.netvision.core.diagnostic.model.DebugCategory;
import java.util.EnumSet;
import java.util.Set;

public class DebugFlag {
  private final Set<DebugCategory> flags = EnumSet.noneOf(DebugCategory.class);

  public void enable(DebugCategory category) {
    flags.add(category);
  }

  public void disable(DebugCategory category) {
    flags.remove(category);
  }

  public boolean isSet(DebugCategory category) {
    return flags.contains(category);
  }

  public void clear() {
    flags.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/api/query/QueryBuilder.java ---

package club.nezxenka.netvision.core.storage.api.query;

public class QueryBuilder {
  public String selectWhere(String table, String column) {
    return "SELECT * FROM "
        + table
        + " WHERE "
        + column
        + " = ? ORDER BY created_at DESC LIMIT ? OFFSET ?";
  }

  public String countWhere(String table, String column) {
    return "SELECT COUNT(*) FROM " + table + " WHERE " + column + " = ?";
  }

  public String deleteWhere(String table, String column) {
    return "DELETE FROM " + table + " WHERE " + column + " = ?";
  }

  public String insertValues(String table, int placeholders) {
    StringBuilder sb = new StringBuilder("INSERT INTO ").append(table).append(" VALUES (");
    for (int i = 0; i < placeholders; i++) {
      if (i > 0) sb.append(", ");
      sb.append("?");
    }
    sb.append(")");
    return sb.toString();
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/api/RecordStorage.java ---

package club.nezxenka.netvision.core.storage.api;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.storage.model.Infraction;
import club.nezxenka.netvision.core.storage.model.PlayerMenuData;
import club.nezxenka.netvision.core.storage.model.ProbabilityEntry;
import java.util.List;
import java.util.Map;
import java.util.UUID;

public interface RecordStorage {
  void logAlert(NetVisionPlayer player, String verbose, String moduleName, int vls);

  int getLogCount(UUID player);

  List<Infraction> getViolations(UUID player, int page, int limit);

  int getUniqueViolatorsSince(long since);

  int getLogCount(long since);

  List<Infraction> getViolations(int page, int limit, long since);

  int getViolationLevel(UUID playerUUID, String punishGroupName);

  int incrementViolationLevel(UUID playerUUID, String punishGroupName);

  void resetViolationLevel(UUID playerUUID, String punishGroupName);

  void resetAllViolationLevels(UUID playerUUID);

  void saveProbability(UUID uuid, String playerName, double probability);

  List<Double> getPlayerProbabilities(UUID uuid, int limit);

  List<ProbabilityEntry> getPlayerProbabilityEntries(UUID uuid, int limit, int offset);

  int getPlayerProbabilityCount(UUID uuid);

  void deletePlayerProbabilities(UUID uuid);

  Map<UUID, PlayerMenuData> getAllOnlinePlayerMenuData();
}


--- src/main/java/club/nezxenka/netvision/core/storage/connection/config/DatabasePathResolver.java ---

package club.nezxenka.netvision.core.storage.connection.config;

import java.io.File;
import org.bukkit.plugin.java.JavaPlugin;

public class DatabasePathResolver {
  private final JavaPlugin plugin;

  public DatabasePathResolver(JavaPlugin plugin) {
    this.plugin = plugin;
  }

  public File resolve(String fileName) {
    return new File(plugin.getDataFolder(), fileName);
  }

  public String jdbcUrl(File dbFile) {
    return "jdbc:sqlite:" + dbFile.getAbsolutePath();
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/connection/DatabaseManager.java ---

package club.nezxenka.netvision.core.storage.connection;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.core.storage.sqlite.SQLiteViolationDatabase;
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import java.io.File;
import lombok.Getter;

@Getter
public class DatabaseManager {
  private final RecordStorage database;
  private final HikariDataSource dataSource;

  public DatabaseManager(NetVision plugin, ConfigManager configManager) {
    this.dataSource = createDataSource(plugin);
    this.database = new SQLiteViolationDatabase(this.dataSource, plugin, configManager);
  }

  private HikariDataSource createDataSource(NetVision plugin) {
    File dbFile = new File(plugin.getDataFolder(), "violations.db");
    HikariConfig config = new HikariConfig();
    config.setPoolName("NetVision-Pool");
    config.setDriverClassName("org.sqlite.JDBC");
    config.setJdbcUrl("jdbc:sqlite:" + dbFile.getAbsolutePath());
    int poolSize = Math.max(2, Math.min(8, Runtime.getRuntime().availableProcessors()));
    config.setMaximumPoolSize(poolSize);
    config.addDataSourceProperty("journal_mode", "WAL");
    config.addDataSourceProperty("synchronous", "NORMAL");
    config.addDataSourceProperty("busy_timeout", "5000");
    config.setConnectionTimeout(30000);
    config.setIdleTimeout(600000);
    config.setMaxLifetime(1800000);
    return new HikariDataSource(config);
  }

  public void shutdown() {
    if (dataSource != null && !dataSource.isClosed()) dataSource.close();
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/connection/pool/ConnectionPoolConfig.java ---

package club.nezxenka.netvision.core.storage.connection.pool;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class ConnectionPoolConfig {
  private String poolName;
  private int maxPoolSize;
  private long connectionTimeout;
  private long idleTimeout;
  private long maxLifetime;
  private String journalMode;
  private String synchronous;
  private int busyTimeout;
}


--- src/main/java/club/nezxenka/netvision/core/storage/model/Infraction.java ---

package club.nezxenka.netvision.core.storage.model;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.Instant;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public record Infraction(
    String serverName,
    UUID playerUUID,
    String playerName,
    String moduleName,
    String verbose,
    int vl,
    Instant createdAt) {
  public static List<Infraction> fromResultSet(ResultSet resultSet) throws SQLException {
    List<Infraction> violations = new ArrayList<>();
    while (resultSet.next()) {
      String server = resultSet.getString("server");
      UUID player = UUID.fromString(resultSet.getString("uuid"));
      String playerName = resultSet.getString("player_name");
      String moduleName = resultSet.getString("check_name");
      String verbose = resultSet.getString("verbose");
      int vl = resultSet.getInt("vl");
      Instant createdAt = Instant.ofEpochMilli(resultSet.getLong("created_at"));
      violations.add(
          new Infraction(server, player, playerName, moduleName, verbose, vl, createdAt));
    }
    return violations;
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/model/mapper/InfractionResultSetMapper.java ---

package club.nezxenka.netvision.core.storage.model.mapper;

import club.nezxenka.netvision.core.storage.model.Infraction;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.Instant;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class InfractionResultSetMapper {

  public List<Infraction> mapAll(ResultSet rs) throws SQLException {
    List<Infraction> violations = new ArrayList<>();
    while (rs.next()) {
      violations.add(
          new Infraction(
              rs.getString("server"),
              UUID.fromString(rs.getString("uuid")),
              rs.getString("player_name"),
              rs.getString("check_name"),
              rs.getString("verbose"),
              rs.getInt("vl"),
              Instant.ofEpochMilli(rs.getLong("created_at"))));
    }
    return violations;
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/model/PlayerMenuData.java ---

package club.nezxenka.netvision.core.storage.model;

import java.util.List;
import java.util.UUID;
import lombok.AllArgsConstructor;
import lombok.Getter;

@Getter
@AllArgsConstructor
public class PlayerMenuData {
  private final UUID uuid;
  private final String playerName;
  private final List<Double> probabilities;
}


--- src/main/java/club/nezxenka/netvision/core/storage/model/ProbabilityEntry.java ---

package club.nezxenka.netvision.core.storage.model;

public record ProbabilityEntry(double probability, long createdAt, String server) {}


--- src/main/java/club/nezxenka/netvision/core/storage/sqlite/migration/SchemaMigration.java ---

package club.nezxenka.netvision.core.storage.sqlite.migration;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

public class SchemaMigration {
  private final List<String> statements = new ArrayList<>();

  public SchemaMigration add(String sql) {
    statements.add(sql);
    return this;
  }

  public void execute(Connection connection) throws SQLException {
    try (Statement stmt = connection.createStatement()) {
      for (String sql : statements) stmt.execute(sql);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/sqlite/query/SqlParameterBinder.java ---

package club.nezxenka.netvision.core.storage.sqlite.query;

import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.UUID;

public class SqlParameterBinder {
  public void bindUuid(PreparedStatement ps, int index, UUID uuid) throws SQLException {
    ps.setString(index, uuid.toString());
  }

  public void bindString(PreparedStatement ps, int index, String value) throws SQLException {
    ps.setString(index, value);
  }

  public void bindInt(PreparedStatement ps, int index, int value) throws SQLException {
    ps.setInt(index, value);
  }

  public void bindLong(PreparedStatement ps, int index, long value) throws SQLException {
    ps.setLong(index, value);
  }

  public void bindDouble(PreparedStatement ps, int index, double value) throws SQLException {
    ps.setDouble(index, value);
  }
}


--- src/main/java/club/nezxenka/netvision/core/storage/sqlite/SQLiteViolationDatabase.java ---

package club.nezxenka.netvision.core.storage.sqlite;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.core.storage.model.Infraction;
import club.nezxenka.netvision.core.storage.model.PlayerMenuData;
import club.nezxenka.netvision.core.storage.model.ProbabilityEntry;
import com.zaxxer.hikari.HikariDataSource;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.logging.Level;

public class SQLiteViolationDatabase implements RecordStorage {
  private final HikariDataSource dataSource;
  private final NetVision plugin;
  private final ConfigManager configManager;

  public SQLiteViolationDatabase(
      HikariDataSource dataSource, NetVision plugin, ConfigManager configManager) {
    this.dataSource = dataSource;
    this.plugin = plugin;
    this.configManager = configManager;
    initTables();
  }

  private void initTables() {
    try (Connection conn = dataSource.getConnection();
        Statement statement = conn.createStatement()) {
      statement.execute(
          "CREATE TABLE IF NOT EXISTS violations(id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, server VARCHAR(255) NOT NULL, uuid CHAR(36) NOT NULL, player_name TEXT NOT NULL, check_name TEXT NOT NULL, verbose TEXT NOT NULL, vl INTEGER NOT NULL, created_at BIGINT NOT NULL);");
      statement.execute("DROP INDEX IF EXISTS idx_violations_uuid;");
      statement.execute(
          "CREATE INDEX IF NOT EXISTS idx_violations_uuid_time ON violations(uuid, created_at DESC);");
      statement.execute(
          "CREATE INDEX IF NOT EXISTS idx_violations_time ON violations(created_at DESC);");
      statement.execute(
          "CREATE TABLE IF NOT EXISTS netvision_punishments (uuid CHAR(36) NOT NULL, punish_group VARCHAR(255) NOT NULL, vl INTEGER NOT NULL, PRIMARY KEY (uuid, punish_group));");
      statement.execute(
          "CREATE TABLE IF NOT EXISTS chicken_coop_probabilities (id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, uuid CHAR(36) NOT NULL, player_name TEXT NOT NULL, probability REAL NOT NULL, created_at BIGINT NOT NULL);");
      statement.execute(
          "CREATE INDEX IF NOT EXISTS idx_coop_uuid_time ON chicken_coop_probabilities(uuid, created_at DESC);");
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to initialize database tables", e);
    }
  }

  @Override
  public void logAlert(NetVisionPlayer player, String verbose, String moduleName, int vls) {
    String sql =
        "INSERT INTO violations (server, uuid, player_name, check_name, verbose, vl, created_at) VALUES (?, ?, ?, ?, ?, ?, ?)";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, configManager.getConfig().getString("history.server-name", "server"));
      ps.setString(2, player.getUuid().toString());
      ps.setString(3, player.getPlayer().getName());
      ps.setString(4, moduleName);
      ps.setString(5, verbose);
      ps.setInt(6, vls);
      ps.setLong(7, System.currentTimeMillis());
      ps.executeUpdate();
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to log violation", e);
    }
  }

  @Override
  public int getLogCount(UUID player) {
    String sql = "SELECT COUNT(*) FROM violations WHERE uuid = ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, player.toString());
      try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) return rs.getInt(1);
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to count violations", e);
    }
    return 0;
  }

  @Override
  public List<Infraction> getViolations(UUID player, int page, int limit) {
    String sql =
        "SELECT * FROM violations WHERE uuid = ? ORDER BY created_at DESC LIMIT ? OFFSET ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, player.toString());
      ps.setInt(2, limit);
      ps.setInt(3, (page - 1) * limit);
      try (ResultSet rs = ps.executeQuery()) {
        return Infraction.fromResultSet(rs);
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to get violations", e);
    }
    return List.of();
  }

  @Override
  public int getLogCount(long since) {
    String sql = "SELECT COUNT(*) FROM violations" + (since > 0 ? " WHERE created_at >= ?" : "");
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      if (since > 0) ps.setLong(1, since);
      try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) return rs.getInt(1);
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to count all violations", e);
    }
    return 0;
  }

  @Override
  public List<Infraction> getViolations(int page, int limit, long since) {
    String sql =
        "SELECT * FROM violations"
            + (since > 0 ? " WHERE created_at >= ?" : "")
            + " ORDER BY created_at DESC LIMIT ? OFFSET ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      int paramIndex = 1;
      if (since > 0) ps.setLong(paramIndex++, since);
      ps.setInt(paramIndex++, limit);
      ps.setInt(paramIndex, (page - 1) * limit);
      try (ResultSet rs = ps.executeQuery()) {
        return Infraction.fromResultSet(rs);
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to get all violations", e);
    }
    return List.of();
  }

  @Override
  public int getViolationLevel(UUID playerUUID, String punishGroupName) {
    String sql = "SELECT vl FROM netvision_punishments WHERE uuid = ? AND punish_group = ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, playerUUID.toString());
      ps.setString(2, punishGroupName);
      try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) return rs.getInt("vl");
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to get violation level for " + playerUUID, e);
    }
    return 0;
  }

  @Override
  public int incrementViolationLevel(UUID playerUUID, String punishGroupName) {
    String upsertSQL =
        "INSERT INTO netvision_punishments (uuid, punish_group, vl) VALUES (?, ?, 1) ON CONFLICT(uuid, punish_group) DO UPDATE SET vl = vl + 1";
    String selectSQL = "SELECT vl FROM netvision_punishments WHERE uuid = ? AND punish_group = ?";
    try (Connection conn = dataSource.getConnection()) {
      conn.setAutoCommit(false);
      try {
        try (PreparedStatement psUpsert = conn.prepareStatement(upsertSQL)) {
          psUpsert.setString(1, playerUUID.toString());
          psUpsert.setString(2, punishGroupName);
          psUpsert.executeUpdate();
        }
        try (PreparedStatement psSelect = conn.prepareStatement(selectSQL)) {
          psSelect.setString(1, playerUUID.toString());
          psSelect.setString(2, punishGroupName);
          try (ResultSet rs = psSelect.executeQuery()) {
            if (rs.next()) {
              int newVl = rs.getInt(1);
              conn.commit();
              return newVl;
            }
          }
        }
        conn.rollback();
        return 0;
      } catch (SQLException e) {
        conn.rollback();
        plugin
            .getLogger()
            .log(Level.SEVERE, "Failed to increment violation level for " + playerUUID, e);
        return 0;
      }
    } catch (SQLException e) {
      plugin
          .getLogger()
          .log(
              Level.SEVERE,
              "Database connection error while incrementing violation level for " + playerUUID,
              e);
      return 0;
    }
  }

  @Override
  public int getUniqueViolatorsSince(long since) {
    String sql = "SELECT COUNT(DISTINCT uuid) FROM violations WHERE created_at >= ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setLong(1, since);
      try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) return rs.getInt(1);
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to count unique violators", e);
    }
    return 0;
  }

  @Override
  public void resetViolationLevel(UUID playerUUID, String punishGroupName) {
    String sql = "DELETE FROM netvision_punishments WHERE uuid = ? AND punish_group = ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, playerUUID.toString());
      ps.setString(2, punishGroupName);
      ps.executeUpdate();
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to reset violation level for " + playerUUID, e);
    }
  }

  @Override
  public void resetAllViolationLevels(UUID playerUUID) {
    String sql = "DELETE FROM netvision_punishments WHERE uuid = ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, playerUUID.toString());
      ps.executeUpdate();
    } catch (SQLException e) {
      plugin
          .getLogger()
          .log(Level.SEVERE, "Failed to reset all violation levels for " + playerUUID, e);
    }
  }

  @Override
  public void saveProbability(UUID uuid, String playerName, double probability) {
    String sql =
        "INSERT INTO chicken_coop_probabilities (uuid, player_name, probability, created_at) VALUES (?, ?, ?, ?)";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, uuid.toString());
      ps.setString(2, playerName);
      ps.setDouble(3, probability);
      ps.setLong(4, System.currentTimeMillis());
      ps.executeUpdate();
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to save probability for " + uuid, e);
    }
  }

  @Override
  public List<ProbabilityEntry> getPlayerProbabilityEntries(UUID uuid, int limit, int offset) {
    List<ProbabilityEntry> entries = new ArrayList<>();
    String serverName = configManager.getConfig().getString("cross-server.server-name", "local");
    String sql =
        "SELECT probability, created_at FROM chicken_coop_probabilities WHERE uuid = ? ORDER BY created_at DESC LIMIT ? OFFSET ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, uuid.toString());
      ps.setInt(2, limit);
      ps.setInt(3, offset);
      try (ResultSet rs = ps.executeQuery()) {
        while (rs.next())
          entries.add(
              new ProbabilityEntry(
                  rs.getDouble("probability"), rs.getLong("created_at"), serverName));
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to get probability entries for " + uuid, e);
    }
    java.util.Collections.reverse(entries);
    return entries;
  }

  @Override
  public int getPlayerProbabilityCount(UUID uuid) {
    String sql = "SELECT COUNT(*) FROM chicken_coop_probabilities WHERE uuid = ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, uuid.toString());
      try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) return rs.getInt(1);
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to count probabilities for " + uuid, e);
    }
    return 0;
  }

  @Override
  public List<Double> getPlayerProbabilities(UUID uuid, int limit) {
    List<Double> probabilities = new ArrayList<>();
    String sql =
        "SELECT probability FROM chicken_coop_probabilities WHERE uuid = ? ORDER BY created_at DESC LIMIT ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, uuid.toString());
      ps.setInt(2, limit);
      try (ResultSet rs = ps.executeQuery()) {
        while (rs.next()) probabilities.add(rs.getDouble("probability"));
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to get probabilities for " + uuid, e);
    }
    java.util.Collections.reverse(probabilities);
    return probabilities;
  }

  @Override
  public void deletePlayerProbabilities(UUID uuid) {
    String sql = "DELETE FROM chicken_coop_probabilities WHERE uuid = ?";
    try (Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
      ps.setString(1, uuid.toString());
      ps.executeUpdate();
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to delete probabilities for " + uuid, e);
    }
  }

  @Override
  public Map<UUID, PlayerMenuData> getAllOnlinePlayerMenuData() {
    Map<UUID, PlayerMenuData> data = new HashMap<>();
    String sql =
        "SELECT uuid, player_name, probability, created_at FROM chicken_coop_probabilities ORDER BY uuid, created_at DESC";
    try (Connection conn = dataSource.getConnection();
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery(sql)) {
      Map<UUID, List<Double>> tempMap = new HashMap<>();
      Map<UUID, String> nameMap = new HashMap<>();
      while (rs.next()) {
        UUID uuid = UUID.fromString(rs.getString("uuid"));
        String playerName = rs.getString("player_name");
        double probability = rs.getDouble("probability");
        tempMap.computeIfAbsent(uuid, k -> new ArrayList<>()).add(probability);
        nameMap.putIfAbsent(uuid, playerName);
      }
      for (Map.Entry<UUID, List<Double>> entry : tempMap.entrySet()) {
        UUID uuid = entry.getKey();
        List<Double> probs = entry.getValue();
        if (probs.size() > 10) probs = probs.subList(0, 10);
        java.util.Collections.reverse(probs);
        data.put(uuid, new PlayerMenuData(uuid, nameMap.get(uuid), probs));
      }
    } catch (SQLException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to get all menu data", e);
    }
    return data;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/aim/internal/accelerator/AccelerationComputer.java ---

package club.nezxenka.netvision.engine.aim.accelerator;

public class AccelerationComputer {
  private float lastDeltaYaw;
  private float lastDeltaPitch;
  private float currentYawAccel;
  private float currentPitchAccel;
  private float lastYawAccel;
  private float lastPitchAccel;

  public void tick(float deltaYaw, float deltaPitch) {
    this.lastYawAccel = this.currentYawAccel;
    this.lastPitchAccel = this.currentPitchAccel;
    this.currentYawAccel = Math.abs(deltaYaw) - Math.abs(this.lastDeltaYaw);
    this.currentPitchAccel = Math.abs(deltaPitch) - Math.abs(this.lastDeltaPitch);
    this.lastDeltaYaw = deltaYaw;
    this.lastDeltaPitch = deltaPitch;
  }

  public float getCurrentYawAccel() {
    return currentYawAccel;
  }

  public float getCurrentPitchAccel() {
    return currentPitchAccel;
  }

  public float getLastYawAccel() {
    return lastYawAccel;
  }

  public float getLastPitchAccel() {
    return lastPitchAccel;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/aim/internal/AimEvaluator.java ---

package club.nezxenka.netvision.engine.aim;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.api.AimModule;
import club.nezxenka.netvision.engine.base.BaseModule;
import club.nezxenka.netvision.engine.model.ModuleInfo;
import club.nezxenka.netvision.util.collection.Pair;
import club.nezxenka.netvision.util.collection.RunningMode;
import club.nezxenka.netvision.util.math.NetVisionMath;
import club.nezxenka.netvision.util.rotation.RotationUpdate;
import lombok.Getter;

@ModuleInfo(name = "AimProcessor_Internal")
@Getter
public class AimEvaluator extends BaseModule implements AimModule {
  private static final int SIGNIFICANT_SAMPLES_THRESHOLD = 15;
  private static final int TOTAL_SAMPLES_THRESHOLD = 80;
  public double sensitivityX;
  public double sensitivityY;
  public double divisorX;
  public double divisorY;
  public double modeX, modeY;
  public double deltaDotsX, deltaDotsY;
  private final RunningMode xRotMode = new RunningMode(TOTAL_SAMPLES_THRESHOLD);
  private final RunningMode yRotMode = new RunningMode(TOTAL_SAMPLES_THRESHOLD);
  private float lastXRot;
  private float lastYRot;
  private float lastDeltaYaw = 0.0f;
  private float lastDeltaPitch = 0.0f;
  private float lastYawAccel = 0.0f;
  private float lastPitchAccel = 0.0f;
  private float currentYawAccel = 0.0f;
  private float currentPitchAccel = 0.0f;

  public AimEvaluator(NetVisionPlayer nvPlayer) {
    super(nvPlayer);
  }

  public static double convertToSensitivity(double var13) {
    double var11 = var13 / 0.15F / 8.0D;
    double var9 = Math.cbrt(var11);
    return (var9 - 0.2f) / 0.6f;
  }

  @Override
  public void process(final RotationUpdate rotationUpdate) {
    float deltaYaw = rotationUpdate.getDeltaYaw();
    float deltaPitch = rotationUpdate.getDeltaPitch();
    float deltaYawAbs = Math.abs(deltaYaw);
    float deltaPitchAbs = Math.abs(deltaPitch);
    this.lastYawAccel = this.currentYawAccel;
    this.lastPitchAccel = this.currentPitchAccel;
    this.currentYawAccel = deltaYawAbs - Math.abs(this.lastDeltaYaw);
    this.currentPitchAccel = deltaPitchAbs - Math.abs(this.lastDeltaPitch);
    this.lastDeltaYaw = deltaYaw;
    this.lastDeltaPitch = deltaPitch;
    this.divisorX = NetVisionMath.gcd(deltaYawAbs, lastXRot);
    if (deltaYawAbs > 0 && deltaYawAbs < 5 && divisorX > NetVisionMath.MINIMUM_DIVISOR) {
      this.xRotMode.add(divisorX);
      this.lastXRot = deltaYawAbs;
    }
    this.divisorY = NetVisionMath.gcd(deltaPitchAbs, lastYRot);
    if (deltaPitchAbs > 0 && deltaPitchAbs < 5 && divisorY > NetVisionMath.MINIMUM_DIVISOR) {
      this.yRotMode.add(divisorY);
      this.lastYRot = deltaPitchAbs;
    }
    if (this.xRotMode.size() > SIGNIFICANT_SAMPLES_THRESHOLD) {
      Pair<Double, Integer> modeResult = this.xRotMode.getMode();
      if (modeResult.second() > SIGNIFICANT_SAMPLES_THRESHOLD) {
        this.modeX = modeResult.first();
        this.sensitivityX = convertToSensitivity(this.modeX);
      }
    }
    if (this.yRotMode.size() > SIGNIFICANT_SAMPLES_THRESHOLD) {
      Pair<Double, Integer> modeResult = this.yRotMode.getMode();
      if (modeResult.second() > SIGNIFICANT_SAMPLES_THRESHOLD) {
        this.modeY = modeResult.first();
        this.sensitivityY = convertToSensitivity(this.modeY);
      }
    }
    if (modeX > 0) this.deltaDotsX = deltaYawAbs / modeX;
    if (modeY > 0) this.deltaDotsY = deltaPitchAbs / modeY;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/aim/internal/calculator/DeltaCalculator.java ---

package club.nezxenka.netvision.engine.aim.calculator;

public class DeltaCalculator {
  public float computeDeltaYaw(float currentYaw, float previousYaw) {
    return currentYaw - previousYaw;
  }

  public float computeDeltaPitch(float currentPitch, float previousPitch) {
    return currentPitch - previousPitch;
  }

  public float computeAbsoluteAcceleration(float currentDelta, float previousDelta) {
    return Math.abs(currentDelta) - Math.abs(previousDelta);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/aim/internal/gcd/GcdResolver.java ---

package club.nezxenka.netvision.engine.aim.gcd;

import club.nezxenka.netvision.util.math.NetVisionMath;

public class GcdResolver {
  public double resolve(double current, double previous) {
    double absCurrent = Math.abs(current);
    if (absCurrent <= 0 || absCurrent >= 5) return 0;
    double gcd = NetVisionMath.gcd(absCurrent, previous);
    return gcd > NetVisionMath.MINIMUM_DIVISOR ? gcd : 0;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/aim/internal/sensitivity/MouseSensitivityEstimator.java ---

package club.nezxenka.netvision.engine.aim.sensitivity;

public class MouseSensitivityEstimator {
  public double estimateFromDivisor(double divisor) {
    double var11 = divisor / 0.15F / 8.0D;
    double var9 = Math.cbrt(var11);
    return (var9 - 0.2f) / 0.6f;
  }

  public double toPercentage(double sensitivity) {
    return sensitivity * 200.0;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/aim/model/dto/RotationSnapshotDTO.java ---

package club.nezxenka.netvision.engine.aim.model.dto;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class RotationSnapshotDTO {
  private double sensitivityX;
  private double sensitivityY;
  private double divisorX;
  private double divisorY;
  private double modeX;
  private double modeY;
}


--- src/main/java/club/nezxenka/netvision/engine/aim/model/SensitivityData.java ---

package club.nezxenka.netvision.engine.aim.model;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class SensitivityData {
  private double sensitivityX;
  private double sensitivityY;
  private double modeX;
  private double modeY;
}


--- src/main/java/club/nezxenka/netvision/engine/api/AimModule.java ---

package club.nezxenka.netvision.engine.api;

import club.nezxenka.netvision.util.rotation.RotationUpdate;

public interface AimModule extends AnalysisModule {
  void process(RotationUpdate rotationUpdate);
}


--- src/main/java/club/nezxenka/netvision/engine/api/AnalysisModule.java ---

package club.nezxenka.netvision.engine.api;

public interface AnalysisModule {
  String getModuleName();
}


--- src/main/java/club/nezxenka/netvision/engine/api/context/PacketInspectionContext.java ---

package club.nezxenka.netvision.engine.api.context;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class PacketInspectionContext {
  private PacketReceiveEvent event;
  private NetVisionPlayer player;
  private boolean cancelled;
}


--- src/main/java/club/nezxenka/netvision/engine/api/context/RotationProcessorSpec.java ---

package club.nezxenka.netvision.engine.api.context;

import club.nezxenka.netvision.util.rotation.RotationUpdate;

public interface RotationProcessorSpec {
  void onRotation(RotationUpdate update);

  boolean isActive();
}


--- src/main/java/club/nezxenka/netvision/engine/api/PacketModule.java ---

package club.nezxenka.netvision.engine.api;

import com.github.retrooper.packetevents.event.PacketReceiveEvent;

public interface PacketModule extends AnalysisModule {
  default void onPacketReceive(PacketReceiveEvent event) {}
}


--- src/main/java/club/nezxenka/netvision/engine/api/Reloadable.java ---

package club.nezxenka.netvision.engine.api;

public interface Reloadable {
  void reload();
}


--- src/main/java/club/nezxenka/netvision/engine/base/BaseModule.java ---

package club.nezxenka.netvision.engine.base;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.api.AnalysisModule;
import club.nezxenka.netvision.engine.model.ModuleInfo;
import lombok.Getter;

@Getter
public abstract class BaseModule implements AnalysisModule {
  protected final NetVisionPlayer nvPlayer;
  private final String moduleName;
  private final String configName;

  public BaseModule(NetVisionPlayer nvPlayer) {
    this.nvPlayer = nvPlayer;
    ModuleInfo data = getClass().getAnnotation(ModuleInfo.class);
    this.moduleName = data.name();
    this.configName = data.configName().equals("DEFAULT") ? data.name() : data.configName();
  }

  protected void flag(String debug) {
    nvPlayer.getEnforcementManager().handleFlag(this, debug);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/base/execution/ExecutionOrder.java ---

package club.nezxenka.netvision.engine.base;

public enum ExecutionOrder {
  FIRST,
  EARLY,
  NORMAL,
  LATE,
  LAST
}


--- src/main/java/club/nezxenka/netvision/engine/coordinator/lookup/ModuleLookup.java ---

package club.nezxenka.netvision.engine.coordinator;

import club.nezxenka.netvision.engine.api.AnalysisModule;
import java.util.Map;

public class ModuleLookup {
  private final Map<Class<? extends AnalysisModule>, AnalysisModule> registry;

  public ModuleLookup(Map<Class<? extends AnalysisModule>, AnalysisModule> registry) {
    this.registry = registry;
  }

  @SuppressWarnings("unchecked")
  public <T extends AnalysisModule> T find(Class<T> type) {
    return (T) registry.get(type);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/coordinator/ModuleCoordinator.java ---

package club.nezxenka.netvision.engine.coordinator;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.engine.aim.AimEvaluator;
import club.nezxenka.netvision.engine.api.AimModule;
import club.nezxenka.netvision.engine.api.AnalysisModule;
import club.nezxenka.netvision.engine.api.PacketModule;
import club.nezxenka.netvision.engine.api.Reloadable;
import club.nezxenka.netvision.engine.network.misc.BrandScanner;
import club.nezxenka.netvision.engine.network.neural.ActionTracker;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.integration.worldguard.WorldGuardManager;
import club.nezxenka.netvision.remote.provider.AIServerProvider;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.util.rotation.RotationUpdate;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import java.util.ArrayList;
import java.util.Collection;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ModuleCoordinator {
  private final NetVisionPlayer nvPlayer;
  private final List<AimModule> aimModules = new ArrayList<>();
  private final List<PacketModule> packetModules = new ArrayList<>();
  private final Map<Class<? extends AnalysisModule>, AnalysisModule> modules = new HashMap<>();

  public ModuleCoordinator(
      NetVisionPlayer player,
      NetVision plugin,
      ConfigManager configManager,
      AIServerProvider aiServerProvider,
      WorldGuardManager worldGuardManager,
      SignalManager alertManager) {
    this.nvPlayer = player;
    registerModule(new AimEvaluator(player));
    registerModule(new ActionTracker(player, configManager));
    registerModule(
        new NeuralAnalyzer(
            player, plugin, aiServerProvider, configManager, worldGuardManager, alertManager));
    registerModule(new BrandScanner(player, configManager, alertManager));
  }

  private void registerModule(AnalysisModule module) {
    modules.put(module.getClass(), module);
    if (module instanceof AimModule aimModule) aimModules.add(aimModule);
    if (module instanceof PacketModule packetModule) packetModules.add(packetModule);
  }

  public void reloadModules() {
    for (AnalysisModule module : modules.values())
      if (module instanceof Reloadable reloadable) reloadable.reload();
  }

  public void onRotationUpdate(RotationUpdate update) {
    if (nvPlayer.isBedrockExempt()) return;
    for (AimModule module : aimModules) module.process(update);
  }

  public void onPacketReceive(PacketReceiveEvent event) {
    if (nvPlayer.isBedrockExempt()) return;
    for (PacketModule module : packetModules) module.onPacketReceive(event);
  }

  @SuppressWarnings("unchecked")
  public <T extends AnalysisModule> T getModule(Class<T> clazz) {
    return (T) modules.get(clazz);
  }

  public Collection<AnalysisModule> getAllModules() {
    return modules.values();
  }
}


--- src/main/java/club/nezxenka/netvision/engine/coordinator/provider/ModuleProvider.java ---

package club.nezxenka.netvision.engine.coordinator;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.integration.worldguard.WorldGuardManager;
import club.nezxenka.netvision.remote.provider.AIServerProvider;
import club.nezxenka.netvision.service.signal.internal.SignalManager;

public class ModuleProvider {
  public static ModuleCoordinator createForPlayer(
      NetVisionPlayer player,
      NetVision plugin,
      ConfigManager configManager,
      AIServerProvider aiServerProvider,
      WorldGuardManager worldGuardManager,
      SignalManager alertManager) {
    return new ModuleCoordinator(
        player, plugin, configManager, aiServerProvider, worldGuardManager, alertManager);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/model/collector/TickCollector.java ---

package club.nezxenka.netvision.engine.model;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import java.util.ArrayDeque;
import java.util.Deque;

public class TickCollector {
  private final Deque<TickSample> buffer = new ArrayDeque<>();
  private final int maxSize;

  public TickCollector(int maxSize) {
    this.maxSize = maxSize;
  }

  public void add(NetVisionPlayer player) {
    buffer.add(new TickSample(player));
    while (buffer.size() > maxSize) buffer.removeFirst();
  }

  public Deque<TickSample> getBuffer() {
    return buffer;
  }

  public int size() {
    return buffer.size();
  }

  public void clear() {
    buffer.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/engine/model/ModuleInfo.java ---

package club.nezxenka.netvision.engine.model;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface ModuleInfo {
  String name();

  String configName() default "DEFAULT";
}


--- src/main/java/club/nezxenka/netvision/engine/model/scanning/AnnotationScanner.java ---

package club.nezxenka.netvision.engine.model;

public class AnnotationScanner {
  public ModuleInfo scan(Class<?> checkClass) {
    return checkClass.getAnnotation(ModuleInfo.class);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/model/snapshot/TickSnapshot.java ---

package club.nezxenka.netvision.engine.model;

import java.util.ArrayList;
import java.util.List;

public class TickSnapshot {
  public static List<TickSample> capture(java.util.Deque<TickSample> source) {
    return new ArrayList<>(source);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/model/TickSample.java ---

package club.nezxenka.netvision.engine.model;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.aim.AimEvaluator;

public class TickSample {
  public final float deltaYaw, deltaPitch;
  public final float accelYaw, accelPitch;
  public final float jerkPitch, jerkYaw;
  public final float gcdErrorYaw, gcdErrorPitch;

  public TickSample(NetVisionPlayer nvPlayer) {
    AimEvaluator aimProcessor = nvPlayer.getModuleCoordinator().getModule(AimEvaluator.class);
    this.deltaYaw = nvPlayer.yaw - nvPlayer.lastYaw;
    this.deltaPitch = nvPlayer.pitch - nvPlayer.lastPitch;
    this.accelYaw = aimProcessor.getCurrentYawAccel();
    this.accelPitch = aimProcessor.getCurrentPitchAccel();
    this.jerkYaw = this.accelYaw - aimProcessor.getLastYawAccel();
    this.jerkPitch = this.accelPitch - aimProcessor.getLastPitchAccel();
    if (aimProcessor.getModeX() > 0) {
      double errorX = Math.abs(this.deltaYaw % aimProcessor.getModeX());
      this.gcdErrorYaw = (float) Math.min(errorX, aimProcessor.getModeX() - errorX);
    } else this.gcdErrorYaw = 0;
    if (aimProcessor.getModeY() > 0) {
      double errorY = Math.abs(this.deltaPitch % aimProcessor.getModeY());
      this.gcdErrorPitch = (float) Math.min(errorY, aimProcessor.getModeY() - errorY);
    } else this.gcdErrorPitch = 0;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/misc/internal/BrandScanner.java ---

package club.nezxenka.netvision.engine.network.misc;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.engine.api.PacketModule;
import club.nezxenka.netvision.engine.base.BaseModule;
import club.nezxenka.netvision.engine.model.ModuleInfo;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.chat.ChatUtil;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import com.github.retrooper.packetevents.PacketEvents;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import com.github.retrooper.packetevents.manager.server.ServerVersion;
import com.github.retrooper.packetevents.protocol.packettype.PacketType;
import com.github.retrooper.packetevents.protocol.player.ClientVersion;
import com.github.retrooper.packetevents.wrapper.configuration.client.WrapperConfigClientPluginMessage;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPluginMessage;
import java.nio.charset.StandardCharsets;
import lombok.Getter;
import net.kyori.adventure.text.Component;

@ModuleInfo(name = "ClientBrand_Internal")
public class BrandScanner extends BaseModule implements PacketModule {

  private static final String CHANNEL =
      PacketEvents.getAPI()
              .getServerManager()
              .getVersion()
              .isNewerThanOrEquals(ServerVersion.V_1_13)
          ? "minecraft:brand"
          : "MC|Brand";
  private final ConfigManager configManager;
  private final SignalManager alertManager;

  @Getter private String brand = "vanilla";

  private boolean hasBrand = false;

  public BrandScanner(
      NetVisionPlayer player, ConfigManager configManager, SignalManager alertManager) {
    super(player);
    this.configManager = configManager;
    this.alertManager = alertManager;
  }

  @Override
  public void onPacketReceive(final PacketReceiveEvent event) {
    if (event.getPacketType() == PacketType.Play.Client.PLUGIN_MESSAGE) {
      WrapperPlayClientPluginMessage packet = new WrapperPlayClientPluginMessage(event);
      handle(packet.getChannelName(), packet.getData());
    } else if (event.getPacketType() == PacketType.Configuration.Client.PLUGIN_MESSAGE) {
      WrapperConfigClientPluginMessage packet = new WrapperConfigClientPluginMessage(event);
      handle(packet.getChannelName(), packet.getData());
    }
  }

  private void handle(String channel, byte[] data) {
    if (!channel.equals(BrandScanner.CHANNEL) || hasBrand) return;
    hasBrand = true;
    if (data.length > 64 || data.length == 0) {
      brand = "invalid (" + data.length + " bytes)";
    } else {
      byte[] brandBytes = new byte[data.length - 1];
      System.arraycopy(data, 1, brandBytes, 0, brandBytes.length);
      brand = new String(brandBytes, StandardCharsets.UTF_8).replace(" (Velocity)", "");
      brand = ChatUtil.stripColor(brand);
    }
    nvPlayer.setBrand(brand);
    if (!configManager.isClientIgnored(brand)) {
      Component component =
          MessageUtil.getMessage(
              Message.BRAND_NOTIFICATION, "player", nvPlayer.getPlayer().getName(), "brand", brand);
      alertManager.send(component, SignalType.BRAND);
    }
    final boolean hasReachExploit =
        brand.contains("forge")
            && nvPlayer.getUser().getClientVersion().isNewerThanOrEquals(ClientVersion.V_1_18_2)
            && nvPlayer.getUser().getClientVersion().isOlderThan(ClientVersion.V_1_19_4);
    if (hasReachExploit && configManager.isDisconnectBlacklistedForge())
      nvPlayer.disconnect(MessageUtil.getMessage(Message.BRAND_DISCONNECT_FORGE));
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/misc/internal/channel/PluginChannelMatcher.java ---

package club.nezxenka.netvision.engine.network.misc.channel;

import com.github.retrooper.packetevents.PacketEvents;
import com.github.retrooper.packetevents.manager.server.ServerVersion;

public class PluginChannelMatcher {
  private static final String MODERN_CHANNEL = "minecraft:brand";
  private static final String LEGACY_CHANNEL = "MC|Brand";

  public String resolveChannel() {
    return PacketEvents.getAPI()
            .getServerManager()
            .getVersion()
            .isNewerThanOrEquals(ServerVersion.V_1_13)
        ? MODERN_CHANNEL
        : LEGACY_CHANNEL;
  }

  public boolean matches(String received, String expected) {
    return received.equals(expected);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/misc/internal/validator/BrandDataValidator.java ---

package club.nezxenka.netvision.engine.network.misc.validator;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.protocol.player.ClientVersion;
import java.nio.charset.StandardCharsets;

public class BrandDataValidator {

  public boolean hasReachExploit(NetVisionPlayer player) {
    return (player.getBrand().contains("forge")
        && player.getUser().getClientVersion().isNewerThanOrEquals(ClientVersion.V_1_18_2)
        && player.getUser().getClientVersion().isOlderThan(ClientVersion.V_1_19_4));
  }

  public boolean isValidLength(byte[] data) {
    return data.length <= 64 && data.length > 0;
  }

  public String extractBrand(byte[] data) {
    byte[] brandBytes = new byte[data.length - 1];
    System.arraycopy(data, 1, brandBytes, 0, brandBytes.length);
    return new String(brandBytes, StandardCharsets.UTF_8).replace(" (Velocity)", "");
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/internal/ActionTracker.java ---

package club.nezxenka.netvision.engine.network.neural;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.engine.api.PacketModule;
import club.nezxenka.netvision.engine.base.BaseModule;
import club.nezxenka.netvision.engine.model.ModuleInfo;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import com.github.retrooper.packetevents.protocol.packettype.PacketType;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientInteractEntity;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;

@ModuleInfo(name = "ActionManager_Internal")
public class ActionTracker extends BaseModule implements PacketModule {
  public ActionTracker(NetVisionPlayer player, ConfigManager configManager) {
    super(player);
    int sequence = configManager.getAiSequence();
    player.ticksSinceAttack = sequence + 1;
  }

  @Override
  public void onPacketReceive(final PacketReceiveEvent event) {
    if (event.getPacketType() == PacketType.Play.Client.INTERACT_ENTITY) {
      WrapperPlayClientInteractEntity action = new WrapperPlayClientInteractEntity(event);
      if (action.getAction() == WrapperPlayClientInteractEntity.InteractAction.ATTACK) {
        PacketEntity entity = nvPlayer.getCompensatedEntities().getEntity(action.getEntityId());
        if (entity == null || entity.isPlayer) nvPlayer.ticksSinceAttack = 0;
      }
    } else if (WrapperPlayClientPlayerFlying.isFlying(event.getPacketType())) {
      nvPlayer.ticksSinceAttack++;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/internal/buffer/BufferController.java ---

package club.nezxenka.netvision.engine.network.neural.buffer;

public class BufferController {
  private double buffer;
  private final double flag;
  private final double resetOnFlag;
  private final double multiplier;
  private final double decrease;

  public BufferController(double flag, double resetOnFlag, double multiplier, double decrease) {
    this.flag = flag;
    this.resetOnFlag = resetOnFlag;
    this.multiplier = multiplier;
    this.decrease = decrease;
  }

  public void increase(double probability) {
    buffer = Math.min(buffer + (probability * multiplier), flag);
  }

  public boolean isAboveFlag() {
    return buffer >= flag;
  }

  public void reset() {
    buffer = Math.max(0, buffer - resetOnFlag);
  }

  public void decay() {
    buffer = Math.max(0, buffer - decrease);
  }

  public double get() {
    return buffer;
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/internal/NeuralAnalyzer.java ---

package club.nezxenka.netvision.engine.network.neural;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.diagnostic.model.DebugCategory;
import club.nezxenka.netvision.engine.api.PacketModule;
import club.nezxenka.netvision.engine.api.Reloadable;
import club.nezxenka.netvision.engine.base.BaseModule;
import club.nezxenka.netvision.engine.model.ModuleInfo;
import club.nezxenka.netvision.engine.model.TickSample;
import club.nezxenka.netvision.integration.worldguard.WorldGuardManager;
import club.nezxenka.netvision.remote.connection.AIServer;
import club.nezxenka.netvision.remote.model.AIResponse;
import club.nezxenka.netvision.remote.provider.AIServerProvider;
import club.nezxenka.netvision.serialize.model.TickDataFB;
import club.nezxenka.netvision.serialize.model.TickDataSequenceFB;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import com.github.retrooper.packetevents.protocol.packettype.PacketType;
import com.google.flatbuffers.FlatBufferBuilder;
import com.google.gson.Gson;
import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import lombok.Getter;

@ModuleInfo(name = "AICheck_Internal")
public class NeuralAnalyzer extends BaseModule implements PacketModule, Reloadable {

  private final NetVision plugin;
  private final AIServerProvider aiServerProvider;
  private final ConfigManager configManager;
  private final WorldGuardManager worldGuardManager;
  private final SignalManager alertManager;
  private static final int MAX_TICK_HISTORY = 200;
  private int step;
  private AIServer aiServer;
  private Deque<TickSample> ticks;
  private final Deque<TickSample> tickHistory =
      new ArrayDeque<>() {
        @Override
        public boolean add(TickSample e) {
          if (this.size() >= MAX_TICK_HISTORY) this.removeFirst();
          return super.add(e);
        }
      };
  private int ticksStep;

  @Getter private double buffer = 0.0;

  @Getter private double lastProbability = 0.0;

  @Getter private int prob90 = 0;

  private boolean aiDamageReductionEnabled;

  public List<TickSample> getTickHistory() {
    return new ArrayList<>(tickHistory);
  }

  private double aiDamageReductionProb;
  private double aiDamageReductionMultiplier;
  private double flag;
  private double bufferResetOnFlag;
  private double bufferMultiplier;
  private double bufferDecrease;
  private double suspiciousAlertBuffer;
  private static final double CHEAT_PROBABILITY = 0.99;
  private static final Gson GSON = new Gson();
  private static final ThreadLocal<FlatBufferBuilder> BUILDER =
      ThreadLocal.withInitial(
          () -> {
            FlatBufferBuilder b = new FlatBufferBuilder(1024);
            return b;
          });

  public NeuralAnalyzer(
      NetVisionPlayer nvPlayer,
      NetVision plugin,
      AIServerProvider aiServerProvider,
      ConfigManager configManager,
      WorldGuardManager worldGuardManager,
      SignalManager alertManager) {
    super(nvPlayer);
    this.plugin = plugin;
    this.aiServerProvider = aiServerProvider;
    this.configManager = configManager;
    this.worldGuardManager = worldGuardManager;
    this.alertManager = alertManager;
    reload();
  }

  @Override
  public void reload() {
    ticks = new ArrayDeque<>();
    ticksStep = 0;
    step = configManager.getAiStep();
    flag = configManager.getAiFlag();
    bufferResetOnFlag = configManager.getAiResetOnFlag();
    bufferMultiplier = configManager.getAiBufferMultiplier();
    bufferDecrease = configManager.getAiBufferDecrease();
    aiDamageReductionEnabled = configManager.isAiDamageReductionEnabled();
    aiDamageReductionProb = configManager.getAiDamageReductionProb();
    aiDamageReductionMultiplier = configManager.getAiDamageReductionMultiplier();
    suspiciousAlertBuffer = configManager.getSuspiciousAlertsBuffer();
    aiServer = aiServerProvider.get();
    if (aiServer == null)
      plugin
          .getLogger()
          .warning(
              "[NeuralAnalyzer] AI server is not available for player "
                  + nvPlayer.getPlayer().getName());
  }

  @Override
  public void onPacketReceive(PacketReceiveEvent event) {
    if (event.getPacketType() != PacketType.Play.Client.PLAYER_POSITION
        && event.getPacketType() != PacketType.Play.Client.PLAYER_POSITION_AND_ROTATION
        && event.getPacketType() != PacketType.Play.Client.PLAYER_ROTATION) return;
    if (aiServer == null) return;
    if (nvPlayer.ticksSinceAttack < configManager.getAiSequence()) return;
    if (aiDamageReductionEnabled
        && configManager.isAiWorldGuardEnabled()
        && worldGuardManager != null
        && worldGuardManager.isPlayerInDisabledRegion(nvPlayer.getPlayer())) return;
    TickSample tickData = new TickSample(nvPlayer);
    ticks.add(tickData);
    tickHistory.add(tickData);
    if (++ticksStep < step) return;
    ticksStep = 0;
    sendData();
  }

  private void sendData() {
    List<TickSample> snapshot = new ArrayList<>(ticks);
    ticks.clear();
    if (snapshot.isEmpty()) return;
    byte[] serialized = serialize(snapshot);
    if (serialized == null) return;
    if (configManager.isAiCollectModeEnabled()) {
      saveToFile(serialized);
      return;
    }
    CompletableFuture<String> future = aiServer.sendRequest(serialized);
    future.thenAccept(this::onResponse).exceptionally(this::onError);
  }

  private void onResponse(String json) {
    try {
      AIResponse response = GSON.fromJson(json, AIResponse.class);
      double probability = response.probability();
      this.lastProbability = probability;
      plugin.getHologramManager().addProbability(nvPlayer.getUuid(), probability);
      plugin
          .getChickenCoopMenu()
          .addOrUpdatePlayer(nvPlayer.getUuid(), nvPlayer.getPlayer().getName(), probability);
      if (probability > 0.9) prob90++;
      else if (probability < 0.1) prob90 = Math.max(0, prob90 - 1);
      if (probability > CHEAT_PROBABILITY)
        buffer = Math.min(buffer + probability * bufferMultiplier, flag);
      else buffer = Math.max(0, buffer - bufferDecrease);
      if (buffer >= flag) {
        flag(getDebugInfo(probability));
        buffer = Math.max(0, buffer - bufferResetOnFlag);
      }
      if (probability > 0.9 && aiDamageReductionEnabled) {
        if (Math.random() < aiDamageReductionProb)
          nvPlayer.setDmgMultiplier(aiDamageReductionMultiplier);
        else nvPlayer.setDmgMultiplier(1.0);
      } else {
        nvPlayer.setDmgMultiplier(1.0);
      }
      if (buffer >= suspiciousAlertBuffer && probability > 0.9) {
        plugin
            .getServer()
            .getScheduler()
            .runTask(
                plugin,
                () ->
                    alertManager.send(
                        MessageUtil.getMessage(
                            Message.SUSPICIOUS_ALERT_TRIGGERED,
                            "player",
                            nvPlayer.getPlayer().getName(),
                            "buffer",
                            String.format("%.1f", buffer)),
                        SignalType.SUSPICIOUS));
      }
      plugin
          .getDebugManager()
          .log(
              DebugCategory.AI_PROBABILITY,
              nvPlayer.getPlayer().getName()
                  + " prob="
                  + String.format("%.4f", probability)
                  + " buffer="
                  + String.format("%.2f", buffer));
    } catch (Exception e) {
      plugin
          .getLogger()
          .warning(
              "[NeuralAnalyzer] Failed to parse AI response for "
                  + nvPlayer.getPlayer().getName()
                  + ": "
                  + e.getMessage());
    }
  }

  private Void onError(Throwable throwable) {
    plugin
        .getDebugManager()
        .log(
            DebugCategory.AI_TIMEOUT,
            nvPlayer.getPlayer().getName() + " timeout/error: " + throwable.getMessage());
    return null;
  }

  private String getDebugInfo(double probability) {
    return String.format("prob=%.4f vl=%.1f", probability, buffer);
  }

  private byte[] serialize(List<TickSample> data) {
    FlatBufferBuilder builder = BUILDER.get();
    try {
      builder.clear();
      int[] tickOffsets = new int[data.size()];
      for (int i = 0; i < data.size(); i++) {
        TickSample td = data.get(i);
        tickOffsets[i] =
            TickDataFB.createTickData(
                builder,
                td.deltaYaw,
                td.deltaPitch,
                td.accelYaw,
                td.accelPitch,
                td.jerkPitch,
                td.jerkYaw,
                td.gcdErrorYaw,
                td.gcdErrorPitch);
      }
      int ticksVector = TickDataSequenceFB.createTicksVector(builder, tickOffsets);
      int root = TickDataSequenceFB.createTickDataSequence(builder, ticksVector);
      TickDataSequenceFB.finishTickDataSequenceBuffer(builder, root);
      return builder.sizedByteArray();
    } catch (Exception e) {
      plugin
          .getLogger()
          .warning("[NeuralAnalyzer] Failed to serialize tick data: " + e.getMessage());
      return null;
    }
  }

  private void saveToFile(byte[] data) {
    String label = configManager.getAiCollectModeLabel();
    File dir = new File(plugin.getDataFolder(), "collected" + File.separator + label);
    if (!dir.exists()) dir.mkdirs();
    File file = new File(dir, System.currentTimeMillis() + ".bin");
    try {
      Files.write(file.toPath(), data);
    } catch (IOException e) {
      plugin
          .getLogger()
          .warning("[NeuralAnalyzer] Failed to save collected data: " + e.getMessage());
    }
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/internal/parser/ResponseDeserializer.java ---

package club.nezxenka.netvision.engine.network.neural.parser;

import club.nezxenka.netvision.remote.model.AIResponse;
import com.google.gson.Gson;

public class ResponseDeserializer {
  private static final Gson GSON = new Gson();

  public AIResponse deserialize(String json) {
    return GSON.fromJson(json, AIResponse.class);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/internal/sender/RequestDispatcher.java ---

package club.nezxenka.netvision.engine.network.neural.sender;

import club.nezxenka.netvision.remote.connection.AIServer;
import java.util.concurrent.CompletableFuture;

public class RequestDispatcher {
  private final AIServer server;

  public RequestDispatcher(AIServer server) {
    this.server = server;
  }

  public CompletableFuture<String> dispatch(byte[] payload) {
    if (server == null)
      return CompletableFuture.failedFuture(new IllegalStateException("No AI server"));
    return server.sendRequest(payload);
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/internal/serializer/FlatBufferEncoder.java ---

package club.nezxenka.netvision.engine.network.neural.serializer;

import club.nezxenka.netvision.engine.model.TickSample;
import club.nezxenka.netvision.serialize.model.TickDataFB;
import club.nezxenka.netvision.serialize.model.TickDataSequenceFB;
import com.google.flatbuffers.FlatBufferBuilder;
import java.util.List;

public class FlatBufferEncoder {
  private static final ThreadLocal<FlatBufferBuilder> BUILDER =
      ThreadLocal.withInitial(() -> new FlatBufferBuilder(1024));

  public byte[] encode(List<TickSample> data) {
    FlatBufferBuilder builder = BUILDER.get();
    builder.clear();
    int[] tickOffsets = new int[data.size()];
    for (int i = 0; i < data.size(); i++) {
      TickSample td = data.get(i);
      tickOffsets[i] =
          TickDataFB.createTickData(
              builder,
              td.deltaYaw,
              td.deltaPitch,
              td.accelYaw,
              td.accelPitch,
              td.jerkPitch,
              td.jerkYaw,
              td.gcdErrorYaw,
              td.gcdErrorPitch);
    }
    int ticksVector = TickDataSequenceFB.createTicksVector(builder, tickOffsets);
    int root = TickDataSequenceFB.createTickDataSequence(builder, ticksVector);
    TickDataSequenceFB.finishTickDataSequenceBuffer(builder, root);
    return builder.sizedByteArray();
  }
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/model/BufferState.java ---

package club.nezxenka.netvision.engine.network.neural.model;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class BufferState {
  private double buffer;
  private double lastProbability;
  private int prob90Count;
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/model/config/ModelParameters.java ---

package club.nezxenka.netvision.engine.network.neural.model.config;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class ModelParameters {
  private double cheatProbability;
  private double legitProbability;
  private double damageReductionProb;
  private double damageReductionMultiplier;
  private int maxTickHistory;
}


--- src/main/java/club/nezxenka/netvision/engine/network/neural/model/probability/ProbabilityEvaluator.java ---

package club.nezxenka.netvision.engine.network.neural.model.probability;

public class ProbabilityEvaluator {
  public boolean isCheating(double probability, double threshold) {
    return probability > threshold;
  }

  public boolean isHighRisk(double probability) {
    return probability > 0.9;
  }

  public boolean isLowRisk(double probability) {
    return probability < 0.1;
  }

  public int updateProb90Counter(int current, double probability) {
    if (probability > 0.9) return current + 1;
    if (probability < 0.1) return Math.max(0, current - 1);
    return current;
  }
}


--- src/main/java/club/nezxenka/netvision/entity/api/movement/EntityMovementProfile.java ---

package club.nezxenka.netvision.entity.api.movement;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class EntityMovementProfile {
  private double posX;
  private double posY;
  private double posZ;
  private float yaw;
  private float pitch;
  private boolean onGround;
}


--- src/main/java/club/nezxenka/netvision/entity/api/PacketEntity.java ---

package club.nezxenka.netvision.entity.api;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import com.github.retrooper.packetevents.protocol.entity.type.EntityTypes;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
import lombok.Getter;
import lombok.Setter;

@Getter
public class PacketEntity {
  private final NetVisionPlayer player;
  private final UUID uuid;
  private final EntityType type;
  public final boolean isLivingEntity;
  public final boolean isPlayer;
  public final boolean isBoat;
  @Setter private PacketEntity riding = null;
  private final List<PacketEntity> passengers = new ArrayList<>(0);

  public PacketEntity(NetVisionPlayer player, UUID uuid, EntityType type) {
    this.player = player;
    this.uuid = uuid;
    this.type = type;
    this.isLivingEntity = EntityTypes.isTypeInstanceOf(type, EntityTypes.LIVINGENTITY);
    this.isPlayer = type == EntityTypes.PLAYER;
    this.isBoat = EntityTypes.isTypeInstanceOf(type, EntityTypes.BOAT);
  }

  public boolean inVehicle() {
    return this.riding != null;
  }

  public void mount(PacketEntity vehicle) {
    if (riding != null) eject();
    vehicle.passengers.add(this);
    this.riding = vehicle;
  }

  public void eject() {
    if (riding != null) riding.passengers.remove(this);
    this.riding = null;
  }
}


--- src/main/java/club/nezxenka/netvision/entity/api/state/EntityState.java ---

package club.nezxenka.netvision.entity.api.state;

public enum EntityState {
  ACTIVE,
  DESPAWNED,
  DEAD,
  REMOVED
}


--- src/main/java/club/nezxenka/netvision/entity/compensation/CompensatedEntities.java ---

package club.nezxenka.netvision.entity.compensation;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import club.nezxenka.netvision.entity.type.animal.PacketEntityHorse;
import club.nezxenka.netvision.entity.type.object.PacketEntityArmorStand;
import club.nezxenka.netvision.entity.type.object.PacketEntityTrackXRot;
import club.nezxenka.netvision.entity.type.player.PacketEntityPlayer;
import club.nezxenka.netvision.entity.type.player.PacketEntitySelf;
import club.nezxenka.netvision.entity.type.projectile.PacketEntityUnHittable;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import com.github.retrooper.packetevents.protocol.entity.type.EntityTypes;
import it.unimi.dsi.fastutil.ints.Int2ObjectOpenHashMap;
import java.util.UUID;

public class CompensatedEntities {
  private final NetVisionPlayer player;
  public final Int2ObjectOpenHashMap<PacketEntity> entityMap = new Int2ObjectOpenHashMap<>();
  public final PacketEntitySelf self;

  public CompensatedEntities(NetVisionPlayer player) {
    this.player = player;
    this.self = new PacketEntitySelf(player);
  }

  public void addEntity(int entityId, UUID uuid, EntityType type) {
    PacketEntity packetEntity;
    if (type == EntityTypes.PLAYER) packetEntity = new PacketEntityPlayer(player, uuid);
    else if (EntityTypes.isTypeInstanceOf(type, EntityTypes.ABSTRACT_HORSE))
      packetEntity = new PacketEntityHorse(player, uuid, type);
    else if (EntityTypes.isTypeInstanceOf(type, EntityTypes.BOAT) || type == EntityTypes.CHICKEN)
      packetEntity = new PacketEntityTrackXRot(player, uuid, type);
    else if (EntityTypes.isTypeInstanceOf(type, EntityTypes.ABSTRACT_ARROW)
        || type == EntityTypes.FIREWORK_ROCKET
        || type == EntityTypes.ITEM) packetEntity = new PacketEntityUnHittable(player, uuid, type);
    else if (type == EntityTypes.ARMOR_STAND)
      packetEntity = new PacketEntityArmorStand(player, uuid, type);
    else packetEntity = new PacketEntity(player, uuid, type);
    entityMap.put(entityId, packetEntity);
  }

  public PacketEntity getEntity(int entityId) {
    return entityId == player.getEntityId() ? self : entityMap.get(entityId);
  }

  public void removeEntity(int entityId) {
    entityMap.remove(entityId);
  }

  public void clear() {
    entityMap.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/entity/compensation/registry/EntityTypeRegistry.java ---

package club.nezxenka.netvision.entity.compensation.registry;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.function.BiFunction;

public class EntityTypeRegistry {
  private final Map<EntityType, BiFunction<NetVisionPlayer, UUID, PacketEntity>> factories =
      new HashMap<>();

  public void register(EntityType type, BiFunction<NetVisionPlayer, UUID, PacketEntity> factory) {
    factories.put(type, factory);
  }

  public PacketEntity create(EntityType type, NetVisionPlayer player, UUID uuid) {
    BiFunction<NetVisionPlayer, UUID, PacketEntity> factory = factories.get(type);
    return factory != null ? factory.apply(player, uuid) : new PacketEntity(player, uuid, type);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/compensation/tracker/EntityLifecycleTracker.java ---

package club.nezxenka.netvision.entity.compensation.tracker;

import java.util.HashSet;
import java.util.Set;

public class EntityLifecycleTracker {
  private final Set<Integer> pendingSpawns = new HashSet<>();
  private final Set<Integer> pendingDespawns = new HashSet<>();

  public void markForSpawn(int entityId) {
    pendingSpawns.add(entityId);
  }

  public void markForDespawn(int entityId) {
    pendingDespawns.add(entityId);
  }

  public boolean isPendingSpawn(int entityId) {
    return pendingSpawns.contains(entityId);
  }

  public boolean isPendingDespawn(int entityId) {
    return pendingDespawns.contains(entityId);
  }

  public void clearSpawn(int entityId) {
    pendingSpawns.remove(entityId);
  }

  public void clearDespawn(int entityId) {
    pendingDespawns.remove(entityId);
  }

  public void clearAll() {
    pendingSpawns.clear();
    pendingDespawns.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/animal/metadata/HorseMetadata.java ---

package club.nezxenka.netvision.entity.type.animal.metadata;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class HorseMetadata {
  private double jumpStrength;
  private double movementSpeed;
  private boolean tamed;
}


--- src/main/java/club/nezxenka/netvision/entity/type/animal/PacketEntityHorse.java ---

package club.nezxenka.netvision.entity.type.animal;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.type.object.PacketEntityTrackXRot;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import java.util.UUID;

public class PacketEntityHorse extends PacketEntityTrackXRot {
  public PacketEntityHorse(NetVisionPlayer player, UUID uuid, EntityType type) {
    super(player, uuid, type);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/object/collision/CollisionBox.java ---

package club.nezxenka.netvision.entity.type.object.collision;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class CollisionBox {
  private double width;
  private double height;
  private double offsetX;
  private double offsetY;
  private double offsetZ;
}


--- src/main/java/club/nezxenka/netvision/entity/type/object/PacketEntityArmorStand.java ---

package club.nezxenka.netvision.entity.type.object;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import java.util.UUID;

public class PacketEntityArmorStand extends PacketEntity {
  public PacketEntityArmorStand(NetVisionPlayer player, UUID uuid, EntityType type) {
    super(player, uuid, type);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/object/PacketEntityTrackXRot.java ---

package club.nezxenka.netvision.entity.type.object;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import java.util.UUID;

public class PacketEntityTrackXRot extends PacketEntity {
  public PacketEntityTrackXRot(NetVisionPlayer player, UUID uuid, EntityType type) {
    super(player, uuid, type);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/player/metadata/PlayerEntityMetadata.java ---

package club.nezxenka.netvision.entity.type.player.metadata;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class PlayerEntityMetadata {
  private String name;
  private boolean sneaking;
  private boolean sprinting;
  private boolean invisible;
}


--- src/main/java/club/nezxenka/netvision/entity/type/player/PacketEntityPlayer.java ---

package club.nezxenka.netvision.entity.type.player;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.protocol.entity.type.EntityTypes;
import java.util.UUID;

public class PacketEntityPlayer extends PacketEntity {
  public PacketEntityPlayer(NetVisionPlayer player, UUID uuid) {
    super(player, uuid, EntityTypes.PLAYER);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/player/PacketEntitySelf.java ---

package club.nezxenka.netvision.entity.type.player;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.protocol.entity.type.EntityTypes;

public class PacketEntitySelf extends PacketEntity {
  public PacketEntitySelf(NetVisionPlayer player) {
    super(player, player.getUuid(), EntityTypes.PLAYER);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/projectile/PacketEntityUnHittable.java ---

package club.nezxenka.netvision.entity.type.projectile;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.entity.api.PacketEntity;
import com.github.retrooper.packetevents.protocol.entity.type.EntityType;
import java.util.UUID;

public class PacketEntityUnHittable extends PacketEntity {
  public PacketEntityUnHittable(NetVisionPlayer player, UUID uuid, EntityType type) {
    super(player, uuid, type);
  }
}


--- src/main/java/club/nezxenka/netvision/entity/type/projectile/trajectory/ProjectileTrajectory.java ---

package club.nezxenka.netvision.entity.type.projectile.trajectory;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class ProjectileTrajectory {
  private double motionX;
  private double motionY;
  private double motionZ;
  private int lifetime;
}


--- src/main/java/club/nezxenka/netvision/event/listener/DamageEventListener.java ---

package club.nezxenka.netvision.event.listener;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.EventPriority;
import org.bukkit.event.Listener;
import org.bukkit.event.entity.EntityDamageByEntityEvent;

public class DamageEventListener implements Listener {
  private final PlayerDataManager playerDataManager;

  public DamageEventListener(PlayerDataManager playerDataManager) {
    this.playerDataManager = playerDataManager;
  }

  @EventHandler(priority = EventPriority.HIGHEST, ignoreCancelled = true)
  public void onEntityDamageByEntity(EntityDamageByEntityEvent event) {
    if (!(event.getDamager() instanceof Player damager)) return;
    NetVisionPlayer nvPlayer = playerDataManager.getPlayer(damager);
    if (nvPlayer == null) return;
    double multiplier = nvPlayer.getDmgMultiplier();
    if (multiplier < 1.0) event.setDamage(event.getDamage() * multiplier);
  }
}


--- src/main/java/club/nezxenka/netvision/event/listener/postprocess/DamagePostprocessor.java ---

package club.nezxenka.netvision.event.listener.postprocess;

import org.bukkit.event.entity.EntityDamageByEntityEvent;

public class DamagePostprocessor {
  public void applyReduction(EntityDamageByEntityEvent event, double multiplier) {
    if (multiplier < 1.0) event.setDamage(event.getDamage() * multiplier);
  }
}


--- src/main/java/club/nezxenka/netvision/event/listener/preprocess/DamagePreprocessor.java ---

package club.nezxenka.netvision.event.listener.preprocess;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import org.bukkit.entity.Player;
import org.bukkit.event.entity.EntityDamageByEntityEvent;

public class DamagePreprocessor {
  private final PlayerDataManager manager;

  public DamagePreprocessor(PlayerDataManager manager) {
    this.manager = manager;
  }

  public NetVisionPlayer resolveDamager(EntityDamageByEntityEvent event) {
    if (!(event.getDamager() instanceof Player damager)) return null;
    return manager.getPlayer(damager);
  }
}


--- src/main/java/club/nezxenka/netvision/integration/geyser/detector/BedrockDetector.java ---

package club.nezxenka.netvision.integration.geyser.detector;

import java.util.UUID;

public class BedrockDetector {
  private static final String BEDROCK_UUID_PREFIX = "00000000-0000-0000-0009";

  public boolean isPrefixMatch(UUID uuid) {
    return uuid.toString().startsWith(BEDROCK_UUID_PREFIX);
  }

  public boolean hasClass(String name) {
    try {
      Class.forName(name);
      return true;
    } catch (ClassNotFoundException e) {
      return false;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/integration/geyser/floodgate/FloodgateDetector.java ---

package club.nezxenka.netvision.integration.geyser.floodgate;

import java.util.UUID;
import org.geysermc.floodgate.api.FloodgateApi;

public class FloodgateDetector {
  private final boolean available;

  public FloodgateDetector() {
    available = hasClass();
  }

  private boolean hasClass() {
    try {
      Class.forName("org.geysermc.floodgate.api.FloodgateApi");
      return true;
    } catch (ClassNotFoundException e) {
      return false;
    }
  }

  public boolean isBedrock(UUID uuid) {
    if (!available) return false;
    try {
      return FloodgateApi.getInstance().isFloodgatePlayer(uuid);
    } catch (Exception e) {
      return false;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/integration/geyser/GeyserUtil.java ---

package club.nezxenka.netvision.integration.geyser;

import java.util.UUID;
import org.geysermc.floodgate.api.FloodgateApi;

public final class GeyserUtil {
  private static final String FLOODGATE_API_CLASS = "org.geysermc.floodgate.api.FloodgateApi";
  private static final String GEYSER_API_CLASS = "org.geysermc.geyser.api.GeyserApi";
  private static final String BEDROCK_UUID_PREFIX = "00000000-0000-0000-0009";
  private static final boolean floodgatePresent;
  private static final boolean geyserPresent;

  static {
    floodgatePresent = hasClass(FLOODGATE_API_CLASS);
    geyserPresent = hasClass(GEYSER_API_CLASS);
  }

  private GeyserUtil() {}

  public static boolean isBedrockPlayer(UUID uuid) {
    return isFloodgateBedrock(uuid)
        || isGeyserBedrock(uuid)
        || uuid.toString().startsWith(BEDROCK_UUID_PREFIX);
  }

  private static boolean isFloodgateBedrock(UUID uuid) {
    if (!floodgatePresent) return false;
    try {
      return FloodgateApi.getInstance().isFloodgatePlayer(uuid);
    } catch (Exception e) {
      return false;
    }
  }

  private static boolean isGeyserBedrock(UUID uuid) {
    if (!geyserPresent) return false;
    try {
      Class<?> geyserApiClass = Class.forName(GEYSER_API_CLASS);
      Object api = geyserApiClass.getMethod("api").invoke(null);
      return (boolean) api.getClass().getMethod("isBedrockPlayer", UUID.class).invoke(api, uuid);
    } catch (Exception e) {
      return false;
    }
  }

  private static boolean hasClass(String name) {
    try {
      Class.forName(name);
      return true;
    } catch (ClassNotFoundException e) {
      return false;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/integration/worldguard/checker/RegionExemptionChecker.java ---

package club.nezxenka.netvision.integration.worldguard.checker;

import com.sk89q.worldguard.protection.regions.ProtectedRegion;
import java.util.List;
import java.util.Locale;

public class RegionExemptionChecker {
  public boolean isAllExempt(
      List<ProtectedRegion> playerRegions, List<String> disabledEntries, String worldName) {
    if (playerRegions.isEmpty() || disabledEntries.isEmpty()) return false;
    return playerRegions.stream()
        .allMatch(
            region ->
                matchesAny(region.getId().toLowerCase(Locale.ROOT), disabledEntries, worldName));
  }

  private boolean matchesAny(String regionId, List<String> entries, String worldName) {
    for (String entry : entries) {
      if (entry.contains(":")) {
        String[] parts = entry.split(":", 2);
        if (regionId.equals(parts[0]) && worldName.equals(parts[1])) return true;
      } else if (regionId.equals(entry)) return true;
    }
    return false;
  }
}


--- src/main/java/club/nezxenka/netvision/integration/worldguard/region/RegionQueryService.java ---

package club.nezxenka.netvision.integration.worldguard.region;

import com.sk89q.worldedit.bukkit.BukkitAdapter;
import com.sk89q.worldedit.math.BlockVector3;
import com.sk89q.worldguard.WorldGuard;
import com.sk89q.worldguard.protection.ApplicableRegionSet;
import com.sk89q.worldguard.protection.managers.RegionManager;
import com.sk89q.worldguard.protection.regions.ProtectedRegion;
import com.sk89q.worldguard.protection.regions.RegionContainer;
import java.util.List;
import org.bukkit.entity.Player;

public class RegionQueryService {
  private final WorldGuard worldGuard;

  public RegionQueryService(WorldGuard worldGuard) {
    this.worldGuard = worldGuard;
  }

  public List<ProtectedRegion> getRegionsAt(Player player) {
    RegionContainer container = worldGuard.getPlatform().getRegionContainer();
    RegionManager regions = container.get(BukkitAdapter.adapt(player.getWorld()));
    if (regions == null) return List.of();
    ApplicableRegionSet set =
        regions.getApplicableRegions(
            BlockVector3.at(
                player.getLocation().getX(),
                player.getLocation().getY(),
                player.getLocation().getZ()));
    return set.getRegions().stream()
        .filter(r -> !r.getId().equalsIgnoreCase("__global__"))
        .toList();
  }
}


--- src/main/java/club/nezxenka/netvision/integration/worldguard/WorldGuardManager.java ---

package club.nezxenka.netvision.integration.worldguard;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import com.sk89q.worldedit.bukkit.BukkitAdapter;
import com.sk89q.worldedit.math.BlockVector3;
import com.sk89q.worldguard.WorldGuard;
import com.sk89q.worldguard.protection.ApplicableRegionSet;
import com.sk89q.worldguard.protection.managers.RegionManager;
import com.sk89q.worldguard.protection.regions.ProtectedRegion;
import com.sk89q.worldguard.protection.regions.RegionContainer;
import java.util.List;
import java.util.Locale;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;

public class WorldGuardManager {

  private final ConfigManager configManager;
  private final boolean worldGuardLoaded;
  private WorldGuard worldGuardInstance;

  public WorldGuardManager(NetVision plugin, ConfigManager configManager) {
    this.configManager = configManager;
    this.worldGuardLoaded = Bukkit.getPluginManager().isPluginEnabled("WorldGuard");
    if (this.worldGuardLoaded) {
      this.worldGuardInstance = WorldGuard.getInstance();
      plugin.getLogger().info("WorldGuard hook enabled.");
    } else plugin.getLogger().info("WorldGuard not found, hook disabled.");
  }

  public boolean isPlayerInDisabledRegion(Player player) {
    if (!worldGuardLoaded) return false;
    List<String> disabledRegions = configManager.getAiDisabledRegions();
    if (disabledRegions == null || disabledRegions.isEmpty()) return false;
    RegionContainer container = worldGuardInstance.getPlatform().getRegionContainer();
    RegionManager regions = container.get(BukkitAdapter.adapt(player.getWorld()));
    if (regions == null) return false;
    ApplicableRegionSet set =
        regions.getApplicableRegions(
            BlockVector3.at(
                player.getLocation().getX(),
                player.getLocation().getY(),
                player.getLocation().getZ()));
    List<ProtectedRegion> playerRegions =
        set.getRegions().stream().filter(r -> !r.getId().equalsIgnoreCase("__global__")).toList();
    if (playerRegions.isEmpty()) return false;
    final String worldName = player.getWorld().getName().toLowerCase(Locale.ROOT);
    return playerRegions.stream()
        .allMatch(
            region -> {
              final String regionId = region.getId().toLowerCase(Locale.ROOT);
              for (String entry : disabledRegions) {
                if (entry.contains(":")) {
                  String[] parts = entry.split(":", 2);
                  String disabledRegionName = parts[0];
                  String disabledWorldName = parts[1];
                  if (regionId.equals(disabledRegionName) && worldName.equals(disabledWorldName))
                    return true;
                } else {
                  if (regionId.equals(entry)) return true;
                }
              }
              return false;
            });
  }
}


--- src/main/java/club/nezxenka/netvision/listener/menu/click/ClickRouter.java ---

package club.nezxenka.netvision.listener.menu.click;

import club.nezxenka.netvision.visual.menu.coop.ChickenCoopMenu;
import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;

public class ClickRouter {

  public ClickRouter(ChickenCoopMenu chickenCoop, HistoryMenu history) {}

  public void route(InventoryClickEvent event, Player player, String title) {
    if (ChickenCoopMenu.isChickenCoopMenu(title)) event.setCancelled(true);
    else if (HistoryMenu.isHistoryMenu(title)) event.setCancelled(true);
  }

  public boolean matchesChickenCoop(String title) {
    return ChickenCoopMenu.isChickenCoopMenu(title);
  }

  public boolean matchesHistory(String title) {
    return HistoryMenu.isHistoryMenu(title);
  }
}


--- src/main/java/club/nezxenka/netvision/listener/menu/close/SessionCleanupHandler.java ---

package club.nezxenka.netvision.listener.menu.close;

import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import org.bukkit.event.inventory.InventoryCloseEvent;

public class SessionCleanupHandler {
  private final HistoryMenu historyMenu;

  public SessionCleanupHandler(HistoryMenu historyMenu) {
    this.historyMenu = historyMenu;
  }

  public void cleanup(InventoryCloseEvent event) {
    if (HistoryMenu.isHistoryMenu(event.getView().getTitle()))
      historyMenu.removeSession(event.getPlayer().getUniqueId());
  }
}


--- src/main/java/club/nezxenka/netvision/listener/menu/MenuClickListener.java ---

package club.nezxenka.netvision.listener.menu;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.visual.menu.coop.ChickenCoopMenu;
import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import java.util.UUID;
import org.bukkit.ChatColor;
import org.bukkit.GameMode;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.event.inventory.InventoryCloseEvent;

public class MenuClickListener implements Listener {
  private final NetVision plugin;
  private final ChickenCoopMenu chickenCoopMenu;
  private final HistoryMenu historyMenu;

  public MenuClickListener(
      NetVision plugin, ChickenCoopMenu chickenCoopMenu, HistoryMenu historyMenu) {
    this.plugin = plugin;
    this.chickenCoopMenu = chickenCoopMenu;
    this.historyMenu = historyMenu;
  }

  @EventHandler
  public void onInventoryClick(InventoryClickEvent event) {
    if (!(event.getWhoClicked() instanceof Player player)) return;
    String title = event.getView().getTitle();
    if (ChickenCoopMenu.isChickenCoopMenu(title)) handleChickenCoopClick(event, player);
    else if (HistoryMenu.isHistoryMenu(title)) handleHistoryClick(event, player);
  }

  @EventHandler
  public void onInventoryClose(InventoryCloseEvent event) {
    if (!(event.getPlayer() instanceof Player)) return;
    String title = event.getView().getTitle();
    if (HistoryMenu.isHistoryMenu(title))
      historyMenu.removeSession(event.getPlayer().getUniqueId());
  }

  private void handleChickenCoopClick(InventoryClickEvent event, Player player) {
    event.setCancelled(true);
    if (event.getCurrentItem() == null || event.getCurrentItem().getType().isAir()) return;
    int slot = event.getRawSlot();
    UUID targetUuid = chickenCoopMenu.getPlayerUuidBySlot(slot);
    if (targetUuid == null) return;
    Player target = plugin.getServer().getPlayer(targetUuid);
    if (target == null || !target.isOnline()) {
      player.sendMessage(ChatColor.RED + "Игрок не в сети!");
      return;
    }
    if (player.getGameMode() != GameMode.SPECTATOR) player.setGameMode(GameMode.SPECTATOR);
    player.teleport(target.getLocation());
    player.sendMessage(
        ChatColor.GREEN + "Вы следите за игроком " + ChatColor.WHITE + target.getName());
    player.closeInventory();
  }

  private void handleHistoryClick(InventoryClickEvent event, Player player) {
    event.setCancelled(true);
    if (event.getCurrentItem() == null || event.getCurrentItem().getType().isAir()) return;
    int slot = event.getRawSlot();
    historyMenu.handleClick(player, slot);
  }
}


--- src/main/java/club/nezxenka/netvision/NetVision.java ---

package club.nezxenka.netvision;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.diagnostic.internal.DebugManager;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.event.listener.DamageEventListener;
import club.nezxenka.netvision.integration.worldguard.WorldGuardManager;
import club.nezxenka.netvision.listener.menu.MenuClickListener;
import club.nezxenka.netvision.protocol.listener.PacketListener;
import club.nezxenka.netvision.remote.provider.AIServerProvider;
import club.nezxenka.netvision.service.bridge.alert.CrossServerAlertService;
import club.nezxenka.netvision.service.bridge.connection.RedisManager;
import club.nezxenka.netvision.service.bridge.suspicious.CrossServerSuspiciousService;
import club.nezxenka.netvision.service.command.framework.CommandFramework;
import club.nezxenka.netvision.service.hologram.internal.HologramManager;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.util.message.MessageUtil;
import club.nezxenka.netvision.visual.menu.coop.ChickenCoopMenu;
import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import com.github.retrooper.packetevents.PacketEvents;
import io.github.retrooper.packetevents.factory.spigot.SpigotPacketEventsBuilder;
import lombok.Getter;
import net.kyori.adventure.platform.bukkit.BukkitAudiences;
import org.bukkit.plugin.java.JavaPlugin;

public final class NetVision extends JavaPlugin {

  private LocaleManager localeManager;
  private AIServerProvider aiServerProvider;
  private WorldGuardManager worldGuardManager;
  private SignalManager alertManager;

  @Getter PlayerDataManager playerDataManager;

  @Getter DatabaseManager databaseManager;

  @Getter private ConfigManager configManager;

  @Getter private ChickenCoopMenu chickenCoopMenu;

  @Getter private HistoryMenu historyMenu;

  @Getter private HologramManager hologramManager;

  @Getter private DebugManager debugManager;

  @Getter private BukkitAudiences adventure;

  private RedisManager redisManager;
  private CrossServerAlertService crossServerAlertService;
  private CrossServerSuspiciousService crossServerSuspiciousService;

  @Override
  public void onLoad() {
    PacketEvents.setAPI(SpigotPacketEventsBuilder.build(this));
    PacketEvents.getAPI().getSettings().checkForUpdates(false).bStats(true);
    PacketEvents.getAPI().load();
  }

  @Override
  public void onEnable() {
    this.adventure = BukkitAudiences.create(this);
    this.configManager = new ConfigManager(this);
    this.localeManager = new LocaleManager(this, configManager);
    this.debugManager = new DebugManager(this, configManager);
    MessageUtil.init(this.localeManager, this.adventure);
    this.databaseManager = new DatabaseManager(this, configManager);
    this.worldGuardManager = new WorldGuardManager(this, configManager);
    this.alertManager = new SignalManager(this, configManager, localeManager, adventure);
    this.aiServerProvider = new AIServerProvider(this, configManager);
    this.chickenCoopMenu = new ChickenCoopMenu(this);
    this.historyMenu = new HistoryMenu(this);
    this.hologramManager = new HologramManager(this);
    this.playerDataManager =
        new PlayerDataManager(
            this,
            alertManager,
            configManager,
            databaseManager,
            this.aiServerProvider,
            worldGuardManager);
    PacketEvents.getAPI()
        .getEventManager()
        .registerListener(new PacketListener(this.playerDataManager));
    PacketEvents.getAPI().init();
    this.redisManager = new RedisManager(configManager, getLogger());
    this.crossServerAlertService =
        new CrossServerAlertService(
            configManager, this.redisManager, alertManager, this, getLogger());
    this.crossServerAlertService.start();
    this.crossServerSuspiciousService =
        new CrossServerSuspiciousService(
            configManager, this.redisManager, playerDataManager, this, getLogger());
    this.crossServerSuspiciousService.start();
    new CommandFramework(
        this,
        alertManager,
        databaseManager,
        configManager,
        localeManager,
        playerDataManager,
        historyMenu);
    getServer().getPluginManager().registerEvents(new DamageEventListener(playerDataManager), this);
    getServer()
        .getPluginManager()
        .registerEvents(new MenuClickListener(this, chickenCoopMenu, historyMenu), this);
  }

  public void reloadPlugin() {
    configManager.reloadConfig();
    localeManager.reload();
    debugManager.reload();
    alertManager.reload();
    aiServerProvider.reload();
    if (playerDataManager != null) {
      playerDataManager.reloadAllPlayers();
    }
  }

  @Override
  public void onDisable() {
    if (hologramManager != null) {
      hologramManager.shutdown();
    }
    if (this.adventure != null) {
      this.adventure.close();
      this.adventure = null;
    }
    if (databaseManager != null) {
      databaseManager.shutdown();
    }
    if (PacketEvents.getAPI().isInitialized()) {
      PacketEvents.getAPI().terminate();
    }
    if (crossServerSuspiciousService != null) {
      crossServerSuspiciousService.shutdown();
    }
    if (crossServerAlertService != null) {
      crossServerAlertService.shutdown();
    }
    if (redisManager != null) {
      redisManager.shutdown();
    }
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/inbound/FlyingPacketHandler.java ---

package club.nezxenka.netvision.protocol.inbound;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.util.rotation.RotationUpdate;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;

public class FlyingPacketHandler {
  public static void processRotation(
      NetVisionPlayer nvPlayer, WrapperPlayClientPlayerFlying packet) {
    boolean ignoreRotation =
        nvPlayer.packetStateData.lastPacketWasOnePointSeventeenDuplicate
            && nvPlayer.packetStateData.ignoreDuplicatePacketRotation;
    if (packet.hasPositionChanged()) {
      nvPlayer.x = packet.getLocation().getX();
      nvPlayer.y = packet.getLocation().getY();
      nvPlayer.z = packet.getLocation().getZ();
      nvPlayer.packetStateData.lastClaimedPosition = packet.getLocation().getPosition();
    }
    if (packet.hasRotationChanged() && !ignoreRotation) {
      float newYaw = packet.getLocation().getYaw();
      float newPitch = packet.getLocation().getPitch();
      float deltaYaw = newYaw - nvPlayer.yaw;
      float deltaPitch = newPitch - nvPlayer.pitch;
      RotationUpdate update = nvPlayer.rotationUpdate;
      update.getFrom().setYaw(nvPlayer.yaw);
      update.getFrom().setPitch(nvPlayer.pitch);
      update.getTo().setYaw(newYaw);
      update.getTo().setPitch(newPitch);
      update.setDeltaYaw(deltaYaw);
      update.setDeltaPitch(deltaPitch);
      nvPlayer.getModuleCoordinator().onRotationUpdate(update);
      nvPlayer.lastYaw = nvPlayer.yaw;
      nvPlayer.lastPitch = nvPlayer.pitch;
      nvPlayer.yaw = newYaw;
      nvPlayer.pitch = newPitch;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/inbound/flying/RotationProcessor.java ---

package club.nezxenka.netvision.protocol.inbound.flying;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.util.rotation.RotationUpdate;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;

public class RotationProcessor {
  public void process(NetVisionPlayer player, WrapperPlayClientPlayerFlying packet) {
    boolean ignore =
        player.packetStateData.lastPacketWasOnePointSeventeenDuplicate
            && player.packetStateData.ignoreDuplicatePacketRotation;
    if (packet.hasPositionChanged()) {
      player.x = packet.getLocation().getX();
      player.y = packet.getLocation().getY();
      player.z = packet.getLocation().getZ();
      player.packetStateData.lastClaimedPosition = packet.getLocation().getPosition();
    }
    if (packet.hasRotationChanged() && !ignore) {
      float newYaw = packet.getLocation().getYaw();
      float newPitch = packet.getLocation().getPitch();
      float deltaYaw = newYaw - player.yaw;
      float deltaPitch = newPitch - player.pitch;
      RotationUpdate update = player.rotationUpdate;
      update.getFrom().setYaw(player.yaw);
      update.getFrom().setPitch(player.pitch);
      update.getTo().setYaw(newYaw);
      update.getTo().setPitch(newPitch);
      update.setDeltaYaw(deltaYaw);
      update.setDeltaPitch(deltaPitch);
      player.getModuleCoordinator().onRotationUpdate(update);
      player.lastYaw = player.yaw;
      player.lastPitch = player.pitch;
      player.yaw = newYaw;
      player.pitch = newPitch;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/inbound/TransactionPacketHandler.java ---

package club.nezxenka.netvision.protocol.inbound;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.util.collection.Pair;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import com.github.retrooper.packetevents.protocol.packettype.PacketType;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPong;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientWindowConfirmation;

public class TransactionPacketHandler {
  public boolean handle(PacketReceiveEvent event, NetVisionPlayer nvPlayer) {
    short id;
    if (event.getPacketType() == PacketType.Play.Client.WINDOW_CONFIRMATION) {
      WrapperPlayClientWindowConfirmation transaction =
          new WrapperPlayClientWindowConfirmation(event);
      id = transaction.getActionId();
      if (id <= 0 && addResponse(nvPlayer, id)) event.setCancelled(true);
      return true;
    } else if (event.getPacketType() == PacketType.Play.Client.PONG) {
      WrapperPlayClientPong pong = new WrapperPlayClientPong(event);
      id = (short) pong.getId();
      if (addResponse(nvPlayer, id)) event.setCancelled(true);
      return true;
    }
    return false;
  }

  private boolean addResponse(NetVisionPlayer player, short id) {
    Pair<Short, Long> data = null;
    boolean hasID = false;
    for (Pair<Short, Long> iterator : player.transactionsSent) {
      if (iterator.first() == id) {
        hasID = true;
        break;
      }
    }
    if (hasID) {
      do {
        data = player.transactionsSent.poll();
        if (data == null) break;
        player.getLastTransactionReceived().incrementAndGet();
      } while (data.first() != id);
      player
          .getLatencyUtils()
          .handleNettySyncTransaction(player.getLastTransactionReceived().get());
    }
    return data != null;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/inbound/transaction/TransactionResponseMatcher.java ---

package club.nezxenka.netvision.protocol.inbound.transaction;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.util.collection.Pair;

public class TransactionResponseMatcher {
  public boolean matchAndConsume(NetVisionPlayer player, short id) {
    Pair<Short, Long> data = null;
    boolean hasID = false;
    for (Pair<Short, Long> it : player.transactionsSent) {
      if (it.first() == id) {
        hasID = true;
        break;
      }
    }
    if (hasID) {
      do {
        data = player.transactionsSent.poll();
        if (data == null) break;
        player.getLastTransactionReceived().incrementAndGet();
      } while (data.first() != id);
      player
          .getLatencyUtils()
          .handleNettySyncTransaction(player.getLastTransactionReceived().get());
    }
    return data != null;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/listener/filter/EventFilter.java ---

package club.nezxenka.netvision.protocol.listener.filter;

import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import org.bukkit.entity.Player;

public class EventFilter {
  public boolean isPlayerEvent(PacketReceiveEvent event) {
    return event.getPlayer() instanceof Player;
  }

  public boolean isPlayServerPacket(Object packetType) {
    return packetType
        instanceof com.github.retrooper.packetevents.protocol.packettype.PacketType.Play.Server;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/listener/PacketListener.java ---

package club.nezxenka.netvision.protocol.listener;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.protocol.inbound.FlyingPacketHandler;
import club.nezxenka.netvision.protocol.inbound.TransactionPacketHandler;
import club.nezxenka.netvision.protocol.outbound.EntitySpawnHandler;
import club.nezxenka.netvision.protocol.outbound.JoinGameHandler;
import club.nezxenka.netvision.protocol.outbound.RespawnHandler;
import club.nezxenka.netvision.protocol.outbound.TeleportHandler;
import club.nezxenka.netvision.protocol.queue.RotationQueueManager;
import club.nezxenka.netvision.protocol.queue.TeleportQueueManager;
import club.nezxenka.netvision.protocol.validation.DuplicatePacketFilter;
import club.nezxenka.netvision.util.collection.Pair;
import com.github.retrooper.packetevents.event.PacketListenerAbstract;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import com.github.retrooper.packetevents.event.PacketSendEvent;
import com.github.retrooper.packetevents.protocol.packettype.PacketType;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerDestroyEntities;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerJoinGame;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerPing;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerPlayerPositionAndLook;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerPlayerRotation;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnEntity;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnLivingEntity;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnPainting;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnPlayer;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerWindowConfirmation;
import org.bukkit.entity.Player;

public class PacketListener extends PacketListenerAbstract {
  private final PlayerDataManager playerDataManager;
  private final TeleportQueueManager teleportQueueManager = new TeleportQueueManager();
  private final RotationQueueManager rotationQueueManager = new RotationQueueManager();
  private final TransactionPacketHandler transactionHandler = new TransactionPacketHandler();
  private final EntitySpawnHandler entitySpawnHandler = new EntitySpawnHandler();
  private final JoinGameHandler joinGameHandler = new JoinGameHandler();
  private final RespawnHandler respawnHandler = new RespawnHandler();
  private final TeleportHandler teleportHandler = new TeleportHandler();
  private final DuplicatePacketFilter duplicateFilter = new DuplicatePacketFilter();

  public PacketListener(PlayerDataManager playerDataManager) {
    this.playerDataManager = playerDataManager;
  }

  @Override
  public void onPacketReceive(PacketReceiveEvent event) {
    if (!(event.getPlayer() instanceof Player)) return;
    NetVisionPlayer nvPlayer = playerDataManager.getPlayer((Player) event.getPlayer());
    if (nvPlayer == null) return;
    if (handleTransaction(event, nvPlayer)) return;
    if (WrapperPlayClientPlayerFlying.isFlying(event.getPacketType()))
      handleFlying(event, nvPlayer);
    if (event.isCancelled()) {
      resetFlags(nvPlayer);
      return;
    }
    if (nvPlayer.packetStateData.lastPacketWasTeleport
        || nvPlayer.packetStateData.lastPacketWasServerRotation)
      updatePlayerState(nvPlayer, new WrapperPlayClientPlayerFlying(event));
    nvPlayer.getModuleCoordinator().onPacketReceive(event);
    resetFlags(nvPlayer);
  }

  private boolean handleTransaction(PacketReceiveEvent event, NetVisionPlayer nvPlayer) {
    return transactionHandler.handle(event, nvPlayer);
  }

  private void handleFlying(PacketReceiveEvent event, NetVisionPlayer nvPlayer) {
    WrapperPlayClientPlayerFlying flying = new WrapperPlayClientPlayerFlying(event);
    boolean teleported = teleportQueueManager.checkQueue(nvPlayer, flying);
    boolean serverRotated = !teleported && rotationQueueManager.checkQueue(nvPlayer, flying);
    nvPlayer.packetStateData.lastPacketWasTeleport = teleported;
    nvPlayer.packetStateData.lastPacketWasServerRotation = serverRotated;
    duplicateFilter.filterDuplicate(nvPlayer, flying, event);
    if (!event.isCancelled()) FlyingPacketHandler.processRotation(nvPlayer, flying);
  }

  private void updatePlayerState(NetVisionPlayer nvPlayer, WrapperPlayClientPlayerFlying flying) {
    if (flying.hasPositionChanged()) {
      nvPlayer.x = flying.getLocation().getX();
      nvPlayer.y = flying.getLocation().getY();
      nvPlayer.z = flying.getLocation().getZ();
    }
    if (flying.hasRotationChanged()) {
      nvPlayer.yaw = flying.getLocation().getYaw();
      nvPlayer.pitch = flying.getLocation().getPitch();
    }
  }

  private void resetFlags(NetVisionPlayer nvPlayer) {
    nvPlayer.packetStateData.lastPacketWasOnePointSeventeenDuplicate = false;
    nvPlayer.packetStateData.lastPacketWasTeleport = false;
    nvPlayer.packetStateData.lastPacketWasServerRotation = false;
  }

  @Override
  public void onPacketSend(PacketSendEvent event) {
    if (!(event.getPlayer() instanceof Player)) return;
    NetVisionPlayer nvPlayer = playerDataManager.getPlayer((Player) event.getPlayer());
    if (nvPlayer == null) return;
    if (!(event.getPacketType() instanceof PacketType.Play.Server)) return;
    final PacketType.Play.Server packetType = (PacketType.Play.Server) event.getPacketType();
    if (packetType == PacketType.Play.Server.WINDOW_CONFIRMATION)
      handleWindowConfirmation(new WrapperPlayServerWindowConfirmation(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.PING)
      handlePing(new WrapperPlayServerPing(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.SPAWN_ENTITY)
      entitySpawnHandler.handleSpawnEntity(new WrapperPlayServerSpawnEntity(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.SPAWN_LIVING_ENTITY)
      entitySpawnHandler.handleSpawnLivingEntity(
          new WrapperPlayServerSpawnLivingEntity(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.SPAWN_PAINTING)
      entitySpawnHandler.handleSpawnPainting(new WrapperPlayServerSpawnPainting(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.SPAWN_PLAYER)
      entitySpawnHandler.handleSpawnPlayer(new WrapperPlayServerSpawnPlayer(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.DESTROY_ENTITIES)
      entitySpawnHandler.handleDestroyEntities(
          new WrapperPlayServerDestroyEntities(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.JOIN_GAME)
      joinGameHandler.handle(new WrapperPlayServerJoinGame(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.RESPAWN) respawnHandler.handle(nvPlayer);
    else if (packetType == PacketType.Play.Server.PLAYER_POSITION_AND_LOOK)
      teleportHandler.handle(new WrapperPlayServerPlayerPositionAndLook(event), nvPlayer);
    else if (packetType == PacketType.Play.Server.PLAYER_ROTATION)
      teleportHandler.handleRotation(new WrapperPlayServerPlayerRotation(event), nvPlayer);
  }

  private void handleWindowConfirmation(
      WrapperPlayServerWindowConfirmation confirmation, NetVisionPlayer nvPlayer) {
    short id = confirmation.getActionId();
    if (id <= 0 && nvPlayer.didWeSendThatTrans.remove(id)) {
      nvPlayer.entitiesDespawnedThisTransaction.clear();
      nvPlayer.transactionsSent.add(new Pair<>(id, System.nanoTime()));
      nvPlayer.getLastTransactionSent().getAndIncrement();
    }
  }

  private void handlePing(WrapperPlayServerPing ping, NetVisionPlayer nvPlayer) {
    int id = ping.getId();
    if (id == (short) id && nvPlayer.didWeSendThatTrans.remove((short) id)) {
      nvPlayer.entitiesDespawnedThisTransaction.clear();
      nvPlayer.transactionsSent.add(new Pair<>((short) id, System.nanoTime()));
      nvPlayer.getLastTransactionSent().getAndIncrement();
    }
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/listener/router/PacketTypeRouter.java ---

package club.nezxenka.netvision.protocol.listener.router;

import com.github.retrooper.packetevents.protocol.packettype.PacketType;

public class PacketTypeRouter {
  public boolean isWindowConfirmation(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.WINDOW_CONFIRMATION;
  }

  public boolean isPing(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.PING;
  }

  public boolean isSpawnEntity(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.SPAWN_ENTITY;
  }

  public boolean isSpawnLivingEntity(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.SPAWN_LIVING_ENTITY;
  }

  public boolean isSpawnPainting(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.SPAWN_PAINTING;
  }

  public boolean isSpawnPlayer(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.SPAWN_PLAYER;
  }

  public boolean isDestroyEntities(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.DESTROY_ENTITIES;
  }

  public boolean isJoinGame(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.JOIN_GAME;
  }

  public boolean isRespawn(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.RESPAWN;
  }

  public boolean isPositionAndLook(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.PLAYER_POSITION_AND_LOOK;
  }

  public boolean isPlayerRotation(PacketType.Play.Server type) {
    return type == PacketType.Play.Server.PLAYER_ROTATION;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/outbound/entity/EntityTracker.java ---

package club.nezxenka.netvision.protocol.outbound.entity;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;

public class EntityTracker {
  public void onSpawn(
      NetVisionPlayer player,
      int entityId,
      java.util.UUID uuid,
      com.github.retrooper.packetevents.protocol.entity.type.EntityType type) {
    if (player.entitiesDespawnedThisTransaction.contains(entityId)) player.sendTransaction();
    player
        .getLatencyUtils()
        .addRealTimeTask(
            player.getLastTransactionSent().get(),
            () -> player.getCompensatedEntities().addEntity(entityId, uuid, type));
  }

  public void onDestroy(NetVisionPlayer player, int[] entityIds) {
    for (int id : entityIds) player.entitiesDespawnedThisTransaction.add(id);
    player
        .getLatencyUtils()
        .addRealTimeTask(
            player.getLastTransactionSent().get() + 1,
            () -> {
              for (int id : entityIds) player.getCompensatedEntities().removeEntity(id);
            });
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/outbound/EntitySpawnHandler.java ---

package club.nezxenka.netvision.protocol.outbound;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.protocol.entity.type.EntityTypes;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerDestroyEntities;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnEntity;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnLivingEntity;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnPainting;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerSpawnPlayer;

public class EntitySpawnHandler {
  public void handleSpawnEntity(WrapperPlayServerSpawnEntity spawn, NetVisionPlayer nvPlayer) {
    if (nvPlayer.entitiesDespawnedThisTransaction.contains(spawn.getEntityId()))
      nvPlayer.sendTransaction();
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get(),
            () ->
                nvPlayer
                    .getCompensatedEntities()
                    .addEntity(
                        spawn.getEntityId(), spawn.getUUID().orElse(null), spawn.getEntityType()));
  }

  public void handleSpawnLivingEntity(
      WrapperPlayServerSpawnLivingEntity spawn, NetVisionPlayer nvPlayer) {
    if (nvPlayer.entitiesDespawnedThisTransaction.contains(spawn.getEntityId()))
      nvPlayer.sendTransaction();
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get(),
            () ->
                nvPlayer
                    .getCompensatedEntities()
                    .addEntity(spawn.getEntityId(), spawn.getEntityUUID(), spawn.getEntityType()));
  }

  public void handleSpawnPainting(WrapperPlayServerSpawnPainting spawn, NetVisionPlayer nvPlayer) {
    if (nvPlayer.entitiesDespawnedThisTransaction.contains(spawn.getEntityId()))
      nvPlayer.sendTransaction();
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get(),
            () ->
                nvPlayer
                    .getCompensatedEntities()
                    .addEntity(spawn.getEntityId(), spawn.getUUID(), EntityTypes.PAINTING));
  }

  public void handleSpawnPlayer(WrapperPlayServerSpawnPlayer spawn, NetVisionPlayer nvPlayer) {
    if (nvPlayer.entitiesDespawnedThisTransaction.contains(spawn.getEntityId()))
      nvPlayer.sendTransaction();
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get(),
            () ->
                nvPlayer
                    .getCompensatedEntities()
                    .addEntity(spawn.getEntityId(), spawn.getUUID(), EntityTypes.PLAYER));
  }

  public void handleDestroyEntities(
      WrapperPlayServerDestroyEntities destroy, NetVisionPlayer nvPlayer) {
    for (int id : destroy.getEntityIds()) nvPlayer.entitiesDespawnedThisTransaction.add(id);
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get() + 1,
            () -> {
              for (int id : destroy.getEntityIds())
                nvPlayer.getCompensatedEntities().removeEntity(id);
            });
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/outbound/JoinGameHandler.java ---

package club.nezxenka.netvision.protocol.outbound;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerJoinGame;

public class JoinGameHandler {
  public void handle(WrapperPlayServerJoinGame join, NetVisionPlayer nvPlayer) {
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get(),
            () -> {
              nvPlayer.setEntityId(join.getEntityId());
              nvPlayer.setGameMode(join.getGameMode());
              nvPlayer.getCompensatedEntities().clear();
            });
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/outbound/RespawnHandler.java ---

package club.nezxenka.netvision.protocol.outbound;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;

public class RespawnHandler {
  public void handle(NetVisionPlayer nvPlayer) {
    nvPlayer
        .getLatencyUtils()
        .addRealTimeTask(
            nvPlayer.getLastTransactionSent().get(),
            () -> nvPlayer.getCompensatedEntities().clear());
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/outbound/TeleportHandler.java ---

package club.nezxenka.netvision.protocol.outbound;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.actor.state.PlayerRotationData;
import club.nezxenka.netvision.actor.state.PlayerTeleportData;
import com.github.retrooper.packetevents.protocol.teleport.RelativeFlag;
import com.github.retrooper.packetevents.util.Vector3d;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerPlayerPositionAndLook;
import com.github.retrooper.packetevents.wrapper.play.server.WrapperPlayServerPlayerRotation;

public class TeleportHandler {
  public void handle(WrapperPlayServerPlayerPositionAndLook wrapper, NetVisionPlayer nvPlayer) {
    nvPlayer.sendTransaction();
    int transactionId = nvPlayer.getLastTransactionSent().get();
    Vector3d location = new Vector3d(wrapper.getX(), wrapper.getY(), wrapper.getZ());
    RelativeFlag flags = wrapper.getRelativeFlags();
    nvPlayer.getPendingTeleports().add(new PlayerTeleportData(location, flags, transactionId));
  }

  public void handleRotation(WrapperPlayServerPlayerRotation wrapper, NetVisionPlayer nvPlayer) {
    nvPlayer.sendTransaction();
    int transactionId = nvPlayer.getLastTransactionSent().get();
    nvPlayer
        .getPendingRotations()
        .add(new PlayerRotationData(wrapper.getYaw(), wrapper.getPitch(), transactionId));
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/outbound/teleport/TeleportTrigger.java ---

package club.nezxenka.netvision.protocol.outbound.teleport;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.actor.state.PlayerRotationData;
import club.nezxenka.netvision.actor.state.PlayerTeleportData;
import com.github.retrooper.packetevents.protocol.teleport.RelativeFlag;
import com.github.retrooper.packetevents.util.Vector3d;

public class TeleportTrigger {
  public void queuePositionLook(
      NetVisionPlayer player, double x, double y, double z, RelativeFlag flags) {
    player.sendTransaction();
    int tid = player.getLastTransactionSent().get();
    player.getPendingTeleports().add(new PlayerTeleportData(new Vector3d(x, y, z), flags, tid));
  }

  public void queueRotation(NetVisionPlayer player, float yaw, float pitch) {
    player.sendTransaction();
    int tid = player.getLastTransactionSent().get();
    player.getPendingRotations().add(new PlayerRotationData(yaw, pitch, tid));
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/queue/manager/QueueCoordinator.java ---

package club.nezxenka.netvision.protocol.queue.manager;

public class QueueCoordinator {
  public boolean isReady(int lastReceived, int transactionId) {
    return lastReceived >= transactionId;
  }

  public boolean isExpired(int lastReceived, int transactionId) {
    return lastReceived > transactionId;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/queue/RotationQueueManager.java ---

package club.nezxenka.netvision.protocol.queue;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.actor.state.PlayerRotationData;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;

public class RotationQueueManager {
  public boolean checkQueue(NetVisionPlayer player, WrapperPlayClientPlayerFlying flying) {
    if (!flying.hasRotationChanged()
        || flying.hasPositionChanged()
        || player.getPendingRotations().isEmpty()) return false;
    PlayerRotationData rotation;
    while ((rotation = player.getPendingRotations().peek()) != null) {
      if (player.getLastTransactionReceived().get() < rotation.getTransactionId()) break;
      if (flying.getLocation().getYaw() == rotation.getYaw()
          && flying.getLocation().getPitch() == rotation.getPitch()) {
        player.getPendingRotations().poll();
        return true;
      }
      if (player.getLastTransactionReceived().get() > rotation.getTransactionId()) {
        player.getPendingRotations().poll();
        continue;
      }
      break;
    }
    return false;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/queue/TeleportQueueManager.java ---

package club.nezxenka.netvision.protocol.queue;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.actor.state.PlayerTeleportData;
import com.github.retrooper.packetevents.protocol.teleport.RelativeFlag;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;

public class TeleportQueueManager {
  public boolean checkQueue(NetVisionPlayer player, WrapperPlayClientPlayerFlying flying) {
    if (!flying.hasPositionChanged() || player.getPendingTeleports().isEmpty()) return false;
    PlayerTeleportData teleport;
    while ((teleport = player.getPendingTeleports().peek()) != null) {
      if (player.getLastTransactionReceived().get() < teleport.getTransactionId()) break;
      com.github.retrooper.packetevents.protocol.world.Location flyingLocation =
          flying.getLocation();
      RelativeFlag flags = teleport.getFlags();
      double expectedX =
          flags.has(RelativeFlag.X)
              ? player.x + teleport.getLocation().getX()
              : teleport.getLocation().getX();
      double expectedY =
          flags.has(RelativeFlag.Y)
              ? player.y + teleport.getLocation().getY()
              : teleport.getLocation().getY();
      double expectedZ =
          flags.has(RelativeFlag.Z)
              ? player.z + teleport.getLocation().getZ()
              : teleport.getLocation().getZ();
      final double epsilon = 1.0E-7;
      if (Math.abs(flyingLocation.getX() - expectedX) < epsilon
          && Math.abs(flyingLocation.getY() - expectedY) < epsilon
          && Math.abs(flyingLocation.getZ() - expectedZ) < epsilon) {
        player.getPendingTeleports().poll();
        return true;
      }
      if (player.getLastTransactionReceived().get() > teleport.getTransactionId()) {
        player.getPendingTeleports().poll();
        continue;
      }
      break;
    }
    return false;
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/validation/checker/MovementThresholdChecker.java ---

package club.nezxenka.netvision.protocol.validation.checker;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.protocol.player.ClientVersion;
import com.github.retrooper.packetevents.util.Vector3d;

public class MovementThresholdChecker {
  public boolean isWithinThreshold(
      NetVisionPlayer player,
      Vector3d lastClaimed,
      com.github.retrooper.packetevents.protocol.world.Location current) {
    double threshold = player.getMovementThreshold();
    return player.getUser().getClientVersion().isNewerThanOrEquals(ClientVersion.V_1_17)
        && lastClaimed.distanceSquared(current.getPosition()) < threshold * threshold;
  }

  public boolean isNewVersion(NetVisionPlayer player) {
    return player.getUser().getClientVersion().isNewerThanOrEquals(ClientVersion.V_1_21);
  }
}


--- src/main/java/club/nezxenka/netvision/protocol/validation/DuplicatePacketFilter.java ---

package club.nezxenka.netvision.protocol.validation;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import com.github.retrooper.packetevents.event.PacketReceiveEvent;
import com.github.retrooper.packetevents.protocol.player.ClientVersion;
import com.github.retrooper.packetevents.wrapper.play.client.WrapperPlayClientPlayerFlying;

public class DuplicatePacketFilter {
  public void filterDuplicate(
      NetVisionPlayer player, WrapperPlayClientPlayerFlying flying, PacketReceiveEvent event) {
    if (player.packetStateData.lastPacketWasTeleport) return;
    if (player.getUser().getClientVersion().isNewerThanOrEquals(ClientVersion.V_1_21)) return;
    final com.github.retrooper.packetevents.protocol.world.Location location = flying.getLocation();
    final double threshold = player.getMovementThreshold();
    final boolean inVehicle = player.getCompensatedEntities().self.getRiding() != null;
    if (!player.packetStateData.lastPacketWasTeleport
        && flying.hasPositionChanged()
        && flying.hasRotationChanged()
        && ((flying.isOnGround() == player.packetStateData.packetPlayerOnGround
                && (player.getUser().getClientVersion().isNewerThanOrEquals(ClientVersion.V_1_17)
                    && player.packetStateData.lastClaimedPosition.distanceSquared(
                            location.getPosition())
                        < threshold * threshold))
            || inVehicle)) {
      if (player.isCancelDuplicatePacket()) event.setCancelled(true);
      player.packetStateData.lastPacketWasOnePointSeventeenDuplicate = true;
      if (!player.packetStateData.ignoreDuplicatePacketRotation) {
        if (player.yaw != location.getYaw() || player.pitch != location.getPitch()) {
          player.lastYaw = player.yaw;
          player.lastPitch = player.pitch;
        }
        player.yaw = location.getYaw();
        player.pitch = location.getPitch();
      }
      player.packetStateData.lastClaimedPosition = location.getPosition();
    }
  }
}


--- src/main/java/club/nezxenka/netvision/remote/connection/AIServer.java ---

package club.nezxenka.netvision.remote.connection;

import club.nezxenka.netvision.NetVision;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpTimeoutException;
import java.time.Duration;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.Executor;
import lombok.Getter;

public final class AIServer {
  private static final Duration CONNECT_TIMEOUT = Duration.ofSeconds(10);
  private static final Duration REQUEST_TIMEOUT = Duration.ofSeconds(5);
  private static volatile long waiting = 0;
  private static final HttpClient HTTP_CLIENT =
      HttpClient.newBuilder()
          .version(HttpClient.Version.HTTP_2)
          .connectTimeout(CONNECT_TIMEOUT)
          .build();
  private final URI serverUri;
  private final String apiKey;
  private final Executor bukkitExecutor;
  private final NetVision plugin;

  public AIServer(NetVision plugin, String url, String apiKey) {
    this.plugin = plugin;
    this.serverUri = URI.create(url);
    this.apiKey = apiKey;
    this.bukkitExecutor = runnable -> plugin.getServer().getScheduler().runTask(plugin, runnable);
  }

  public CompletableFuture<String> sendRequest(byte[] playerData) {
    if (System.currentTimeMillis() < waiting)
      return CompletableFuture.failedFuture(
          new RequestException(ResponseCode.WAITING, "Waiting 15 sec"));
    HttpRequest request =
        HttpRequest.newBuilder(serverUri)
            .header("Content-Type", "application/octet-stream")
            .header("User-Agent", "NetVision/" + plugin.getDescription().getVersion())
            .header("X-API-Key", this.apiKey)
            .header("Accept", "application/json")
            .POST(HttpRequest.BodyPublishers.ofByteArray(playerData))
            .timeout(REQUEST_TIMEOUT)
            .build();
    return HTTP_CLIENT
        .sendAsync(request, HttpResponse.BodyHandlers.ofString())
        .thenApplyAsync(this::catchResponse, bukkitExecutor)
        .exceptionallyComposeAsync(this::catchException, bukkitExecutor);
  }

  private String catchResponse(HttpResponse<String> response) {
    final int statusCode = response.statusCode();
    if (statusCode >= 500 || statusCode == 403) waiting = System.currentTimeMillis() + 15000;
    if (statusCode >= 300 || statusCode < 200)
      throw new RequestException(
          ResponseCode.fromStatusCode(statusCode),
          "HTTP Status " + statusCode + ": " + response.body());
    return response.body();
  }

  private <U> CompletableFuture<U> catchException(Throwable throwable) {
    final Throwable cause =
        (throwable instanceof java.util.concurrent.CompletionException
                && throwable.getCause() != null)
            ? throwable.getCause()
            : throwable;
    if (cause instanceof RequestException) return CompletableFuture.failedFuture(cause);
    final boolean isTimeout = cause instanceof HttpTimeoutException;
    if (!isTimeout) waiting = System.currentTimeMillis() + 15000;
    final ResponseCode code = isTimeout ? ResponseCode.TIMEOUT : ResponseCode.NETWORK_ERROR;
    return CompletableFuture.failedFuture(
        new RequestException(code, "Request failed: " + cause.getMessage(), cause));
  }

  public enum ResponseCode {
    SUCCESS(200),
    BAD_REQUEST(400),
    UNAUTHORIZED(403),
    INVALID_SEQUENCE(422),
    SERVER_ERROR(500),
    TIMEOUT(-1),
    NETWORK_ERROR(-2),
    PARSE_ERROR(-3),
    WAITING(-5),
    UNKNOWN_ERROR(-4);
    private final int httpCode;

    ResponseCode(int httpCode) {
      this.httpCode = httpCode;
    }

    public static ResponseCode fromStatusCode(int code) {
      for (ResponseCode value : values()) if (value.httpCode == code) return value;
      return code >= 500 ? SERVER_ERROR : (code >= 400 ? BAD_REQUEST : UNKNOWN_ERROR);
    }
  }

  public static final class RequestException extends RuntimeException {
    @Getter private final ResponseCode code;

    public RequestException(ResponseCode code, String message) {
      super(message);
      this.code = code;
    }

    public RequestException(ResponseCode code, String message, Throwable cause) {
      super(message, cause);
      this.code = code;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/remote/connection/client/HttpClientProvider.java ---

package club.nezxenka.netvision.remote.connection.client;

import java.net.http.HttpClient;
import java.time.Duration;

public class HttpClientProvider {
  private static final HttpClient INSTANCE =
      HttpClient.newBuilder()
          .version(HttpClient.Version.HTTP_2)
          .connectTimeout(Duration.ofSeconds(10))
          .build();

  public HttpClient get() {
    return INSTANCE;
  }
}


--- src/main/java/club/nezxenka/netvision/remote/connection/timeout/TimeoutManager.java ---

package club.nezxenka.netvision.remote.connection.timeout;

public class TimeoutManager {
  private static volatile long waitingUntil;

  public boolean isBlocked() {
    return System.currentTimeMillis() < waitingUntil;
  }

  public void block(long millis) {
    waitingUntil = System.currentTimeMillis() + millis;
  }

  public static void reset() {
    waitingUntil = 0;
  }
}


--- src/main/java/club/nezxenka/netvision/remote/model/AIResponse.java ---

package club.nezxenka.netvision.remote.model;

import com.google.gson.annotations.SerializedName;

public record AIResponse(@SerializedName("probability") double probability) {}


--- src/main/java/club/nezxenka/netvision/remote/model/code/StatusCodeMapper.java ---

package club.nezxenka.netvision.remote.model.code;

import club.nezxenka.netvision.remote.connection.AIServer;

public class StatusCodeMapper {
  public AIServer.ResponseCode map(int httpCode) {
    return AIServer.ResponseCode.fromStatusCode(httpCode);
  }

  public boolean isServerError(int code) {
    return code >= 500;
  }

  public boolean isClientError(int code) {
    return code >= 400 && code < 500;
  }
}


--- src/main/java/club/nezxenka/netvision/remote/provider/AIServerProvider.java ---

package club.nezxenka.netvision.remote.provider;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.remote.connection.AIServer;
import java.util.function.Supplier;

public class AIServerProvider implements Supplier<AIServer> {
  private final NetVision plugin;
  private final ConfigManager configManager;
  private AIServer currentInstance;

  public AIServerProvider(NetVision plugin, ConfigManager configManager) {
    this.plugin = plugin;
    this.configManager = configManager;
    this.reload();
  }

  public void reload() {
    if (configManager.isAiEnabled()) {
      String url = configManager.getAiServerUrl();
      String key = configManager.getAiApiKey();
      if (url == null || url.isEmpty() || key == null || key.equals("API-KEY")) {
        plugin.getLogger().warning("[NeuralAnalyzer] AI is enabled but not configured.");
        this.currentInstance = null;
      } else {
        plugin.getLogger().info("[NeuralAnalyzer] AI Check loaded.");
        this.currentInstance = new AIServer(plugin, url, key);
      }
    } else {
      plugin.getLogger().info("[NeuralAnalyzer] AI Check disabled.");
      this.currentInstance = null;
    }
  }

  @Override
  public AIServer get() {
    return this.currentInstance;
  }
}


--- src/main/java/club/nezxenka/netvision/remote/provider/lifecycle/ServerProviderLifecycle.java ---

package club.nezxenka.netvision.remote.provider.lifecycle;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.remote.connection.AIServer;
import java.util.logging.Logger;

public class ServerProviderLifecycle {
  private final NetVision plugin;
  private final ConfigManager configManager;
  private final Logger logger;

  public ServerProviderLifecycle(NetVision plugin, ConfigManager configManager, Logger logger) {
    this.plugin = plugin;
    this.configManager = configManager;
    this.logger = logger;
  }

  public AIServer createIfEnabled() {
    if (!configManager.isAiEnabled()) {
      logger.info("[NeuralAnalyzer] AI Check disabled.");
      return null;
    }
    String url = configManager.getAiServerUrl();
    String key = configManager.getAiApiKey();
    if (url == null || url.isEmpty() || key == null || key.equals("API-KEY")) {
      logger.warning("[NeuralAnalyzer] AI is enabled but not configured.");
      return null;
    }
    logger.info("[NeuralAnalyzer] AI Check loaded.");
    return new AIServer(plugin, url, key);
  }
}


--- src/main/java/club/nezxenka/netvision/remote/restore/DataRestorer.java ---

package club.nezxenka.netvision.remote.restore;

import club.nezxenka.netvision.engine.model.TickSample;
import java.io.File;
import java.io.IOException;
import java.io.PrintWriter;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.format.DateTimeFormatter;
import java.util.List;
import java.util.logging.Level;
import org.bukkit.plugin.java.JavaPlugin;

public class DataRestorer {

  private final JavaPlugin plugin;
  private final File restoredDataFolder;

  public DataRestorer(JavaPlugin plugin) {
    this.plugin = plugin;
    this.restoredDataFolder = new File(plugin.getDataFolder(), "restored_data");
    if (!restoredDataFolder.exists()) restoredDataFolder.mkdirs();
  }

  public boolean restoreData(String playerName, List<TickSample> history) {
    if (history == null || history.isEmpty()) return false;
    String timestamp =
        LocalDateTime.now(ZoneId.systemDefault())
            .format(DateTimeFormatter.ofPattern("yyyyMMdd_HHmmss"));
    String fileName = playerName + "_" + timestamp + ".csv";
    File file = new File(restoredDataFolder, fileName);
    try (PrintWriter writer =
        new PrintWriter(Files.newBufferedWriter(file.toPath(), StandardCharsets.UTF_8))) {
      writer.println(
          "is_cheating,delta_yaw,delta_pitch,accel_yaw,accel_pitch,jerk_yaw,jerk_pitch,gcd_error_yaw,gcd_error_pitch");
      for (TickSample tick : history)
        writer.println(
            "0,"
                + String.format("%.6f", tick.deltaYaw)
                + ","
                + String.format("%.6f", tick.deltaPitch)
                + ","
                + String.format("%.6f", tick.accelYaw)
                + ","
                + String.format("%.6f", tick.accelPitch)
                + ","
                + String.format("%.6f", tick.jerkYaw)
                + ","
                + String.format("%.6f", tick.jerkPitch)
                + ","
                + String.format("%.6f", tick.gcdErrorYaw)
                + ","
                + String.format("%.6f", tick.gcdErrorPitch));
      return true;
    } catch (IOException e) {
      plugin.getLogger().log(Level.SEVERE, "Failed to restore data for " + playerName, e);
      return false;
    }
  }

  public File getRestoredDataFolder() {
    return restoredDataFolder;
  }
}


--- src/main/java/club/nezxenka/netvision/remote/restore/writer/CsvDataWriter.java ---

package club.nezxenka.netvision.remote.restore.writer;

import club.nezxenka.netvision.engine.model.TickSample;
import java.io.PrintWriter;
import java.util.List;

public class CsvDataWriter {
  public void write(PrintWriter writer, List<TickSample> ticks) {
    for (TickSample tick : ticks) {
      writer.println(
          "0,"
              + String.format("%.6f", tick.deltaYaw)
              + ","
              + String.format("%.6f", tick.deltaPitch)
              + ","
              + String.format("%.6f", tick.accelYaw)
              + ","
              + String.format("%.6f", tick.accelPitch)
              + ","
              + String.format("%.6f", tick.jerkYaw)
              + ","
              + String.format("%.6f", tick.jerkPitch)
              + ","
              + String.format("%.6f", tick.gcdErrorYaw)
              + ","
              + String.format("%.6f", tick.gcdErrorPitch));
    }
  }
}


--- src/main/java/club/nezxenka/netvision/serialize/model/builder/FlatBufferBuilderProvider.java ---

package club.nezxenka.netvision.serialize.model.builder;

import com.google.flatbuffers.FlatBufferBuilder;

public class FlatBufferBuilderProvider {
  private static final ThreadLocal<FlatBufferBuilder> BUILDER =
      ThreadLocal.withInitial(() -> new FlatBufferBuilder(1024));

  public FlatBufferBuilder acquire() {
    return BUILDER.get();
  }

  public void reset(FlatBufferBuilder builder) {
    builder.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/serialize/model/schema/FlatBufferSchema.java ---

package club.nezxenka.netvision.serialize.model.schema;

public class FlatBufferSchema {
  public static final int TICK_DATA_FIELDS = 8;
  public static final int SEQUENCE_FIELDS = 1;
  public static final int DEFAULT_BUILDER_SIZE = 1024;
}


--- src/main/java/club/nezxenka/netvision/serialize/model/TickDataFB.java ---

package club.nezxenka.netvision.serialize.model;

import com.google.flatbuffers.BaseVector;
import com.google.flatbuffers.Constants;
import com.google.flatbuffers.FlatBufferBuilder;
import com.google.flatbuffers.Table;
import java.nio.ByteBuffer;
import java.nio.ByteOrder;

@SuppressWarnings("unused")
public final class TickDataFB extends Table {

  public static void ValidateVersion() {
    Constants.FLATBUFFERS_25_2_10();
  }

  public static TickDataFB getRootAsTickData(ByteBuffer _bb) {
    return getRootAsTickData(_bb, new TickDataFB());
  }

  public static TickDataFB getRootAsTickData(ByteBuffer _bb, TickDataFB obj) {
    _bb.order(ByteOrder.LITTLE_ENDIAN);
    return obj.__assign(_bb.getInt(_bb.position()) + _bb.position(), _bb);
  }

  public void __init(int _i, ByteBuffer _bb) {
    __reset(_i, _bb);
  }

  public TickDataFB __assign(int _i, ByteBuffer _bb) {
    __init(_i, _bb);
    return this;
  }

  public float deltaYaw() {
    int o = __offset(4);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float deltaPitch() {
    int o = __offset(6);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float accelYaw() {
    int o = __offset(8);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float accelPitch() {
    int o = __offset(10);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float jerkPitch() {
    int o = __offset(12);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float jerkYaw() {
    int o = __offset(14);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float gcdErrorYaw() {
    int o = __offset(16);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public float gcdErrorPitch() {
    int o = __offset(18);
    return o != 0 ? bb.getFloat(o + bb_pos) : 0.0f;
  }

  public static int createTickData(
      FlatBufferBuilder builder,
      float deltaYaw,
      float deltaPitch,
      float accelYaw,
      float accelPitch,
      float jerkPitch,
      float jerkYaw,
      float gcdErrorYaw,
      float gcdErrorPitch) {
    builder.startTable(8);
    TickDataFB.addGcdErrorPitch(builder, gcdErrorPitch);
    TickDataFB.addGcdErrorYaw(builder, gcdErrorYaw);
    TickDataFB.addJerkYaw(builder, jerkYaw);
    TickDataFB.addJerkPitch(builder, jerkPitch);
    TickDataFB.addAccelPitch(builder, accelPitch);
    TickDataFB.addAccelYaw(builder, accelYaw);
    TickDataFB.addDeltaPitch(builder, deltaPitch);
    TickDataFB.addDeltaYaw(builder, deltaYaw);
    return TickDataFB.endTickData(builder);
  }

  public static void startTickData(FlatBufferBuilder builder) {
    builder.startTable(8);
  }

  public static void addDeltaYaw(FlatBufferBuilder builder, float deltaYaw) {
    builder.addFloat(0, deltaYaw, 0.0f);
  }

  public static void addDeltaPitch(FlatBufferBuilder builder, float deltaPitch) {
    builder.addFloat(1, deltaPitch, 0.0f);
  }

  public static void addAccelYaw(FlatBufferBuilder builder, float accelYaw) {
    builder.addFloat(2, accelYaw, 0.0f);
  }

  public static void addAccelPitch(FlatBufferBuilder builder, float accelPitch) {
    builder.addFloat(3, accelPitch, 0.0f);
  }

  public static void addJerkPitch(FlatBufferBuilder builder, float jerkPitch) {
    builder.addFloat(4, jerkPitch, 0.0f);
  }

  public static void addJerkYaw(FlatBufferBuilder builder, float jerkYaw) {
    builder.addFloat(5, jerkYaw, 0.0f);
  }

  public static void addGcdErrorYaw(FlatBufferBuilder builder, float gcdErrorYaw) {
    builder.addFloat(6, gcdErrorYaw, 0.0f);
  }

  public static void addGcdErrorPitch(FlatBufferBuilder builder, float gcdErrorPitch) {
    builder.addFloat(7, gcdErrorPitch, 0.0f);
  }

  public static int endTickData(FlatBufferBuilder builder) {
    int o = builder.endTable();
    return o;
  }

  public static final class Vector extends BaseVector {

    public Vector __assign(int _vector, int _element_size, ByteBuffer _bb) {
      __reset(_vector, _element_size, _bb);
      return this;
    }

    public TickDataFB get(int j) {
      return get(new TickDataFB(), j);
    }

    public TickDataFB get(TickDataFB obj, int j) {
      return obj.__assign(__indirect(__element(j), bb), bb);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/serialize/model/TickDataSequenceFB.java ---

package club.nezxenka.netvision.serialize.model;

import com.google.flatbuffers.BaseVector;
import com.google.flatbuffers.Constants;
import com.google.flatbuffers.FlatBufferBuilder;
import com.google.flatbuffers.Table;
import java.nio.ByteBuffer;
import java.nio.ByteOrder;

public final class TickDataSequenceFB extends Table {

  public static void ValidateVersion() {
    Constants.FLATBUFFERS_25_2_10();
  }

  public static TickDataSequenceFB getRootAsTickDataSequence(ByteBuffer _bb) {
    return getRootAsTickDataSequence(_bb, new TickDataSequenceFB());
  }

  public static TickDataSequenceFB getRootAsTickDataSequence(
      ByteBuffer _bb, TickDataSequenceFB obj) {
    _bb.order(ByteOrder.LITTLE_ENDIAN);
    return obj.__assign(_bb.getInt(_bb.position()) + _bb.position(), _bb);
  }

  public void __init(int _i, ByteBuffer _bb) {
    __reset(_i, _bb);
  }

  public TickDataSequenceFB __assign(int _i, ByteBuffer _bb) {
    __init(_i, _bb);
    return this;
  }

  public TickDataFB ticks(int j) {
    return ticks(new TickDataFB(), j);
  }

  public TickDataFB ticks(TickDataFB obj, int j) {
    int o = __offset(4);
    return o != 0 ? obj.__assign(__indirect(__vector(o) + j * 4), bb) : null;
  }

  public int ticksLength() {
    int o = __offset(4);
    return o != 0 ? __vector_len(o) : 0;
  }

  public TickDataFB.Vector ticksVector() {
    return ticksVector(new TickDataFB.Vector());
  }

  public TickDataFB.Vector ticksVector(TickDataFB.Vector obj) {
    int o = __offset(4);
    return o != 0 ? obj.__assign(__vector(o), 4, bb) : null;
  }

  public static int createTickDataSequence(FlatBufferBuilder builder, int ticksOffset) {
    builder.startTable(1);
    TickDataSequenceFB.addTicks(builder, ticksOffset);
    return TickDataSequenceFB.endTickDataSequence(builder);
  }

  public static void startTickDataSequence(FlatBufferBuilder builder) {
    builder.startTable(1);
  }

  public static void addTicks(FlatBufferBuilder builder, int ticksOffset) {
    builder.addOffset(0, ticksOffset, 0);
  }

  public static int createTicksVector(FlatBufferBuilder builder, int[] data) {
    builder.startVector(4, data.length, 4);
    for (int i = data.length - 1; i >= 0; i--) builder.addOffset(data[i]);
    return builder.endVector();
  }

  public static void startTicksVector(FlatBufferBuilder builder, int numElems) {
    builder.startVector(4, numElems, 4);
  }

  public static int endTickDataSequence(FlatBufferBuilder builder) {
    int o = builder.endTable();
    return o;
  }

  public static void finishTickDataSequenceBuffer(FlatBufferBuilder builder, int offset) {
    builder.finish(offset);
  }

  public static void finishSizePrefixedTickDataSequenceBuffer(
      FlatBufferBuilder builder, int offset) {
    builder.finishSizePrefixed(offset);
  }

  public static final class Vector extends BaseVector {

    public Vector __assign(int _vector, int _element_size, ByteBuffer _bb) {
      __reset(_vector, _element_size, _bb);
      return this;
    }

    public TickDataSequenceFB get(int j) {
      return get(new TickDataSequenceFB(), j);
    }

    public TickDataSequenceFB get(TickDataSequenceFB obj, int j) {
      return obj.__assign(__indirect(__element(j), bb), bb);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/alert/CrossServerAlertService.java ---

package club.nezxenka.netvision.service.bridge.alert;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.service.bridge.alert.model.CrossServerAlert;
import club.nezxenka.netvision.service.bridge.connection.RedisManager;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.EnumSet;
import java.util.Set;
import java.util.UUID;
import java.util.logging.Level;
import java.util.logging.Logger;
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.serializer.gson.GsonComponentSerializer;

public class CrossServerAlertService implements CrossServerPublisher {
  private static final String DEFAULT_SERVER_NAME = "server-1";
  private static final String DEFAULT_CHANNEL = "netvision:alerts";
  private static final GsonComponentSerializer COMPONENT_SERIALIZER =
      GsonComponentSerializer.gson();
  private final ConfigManager configManager;
  private final RedisManager redisManager;
  private final SignalManager alertManager;
  private final NetVision plugin;
  private final Logger logger;
  private final String origin = UUID.randomUUID().toString();
  private final ObjectMapper mapper = new ObjectMapper();
  private boolean enabled = false;
  private Set<SignalType> mirroredTypes = EnumSet.noneOf(SignalType.class);
  private String serverName = DEFAULT_SERVER_NAME;
  private String channel = DEFAULT_CHANNEL;

  public CrossServerAlertService(
      ConfigManager configManager,
      RedisManager redisManager,
      SignalManager alertManager,
      NetVision plugin,
      Logger logger) {
    this.configManager = configManager;
    this.redisManager = redisManager;
    this.alertManager = alertManager;
    this.plugin = plugin;
    this.logger = logger;
  }

  public void start() {
    if (!configManager.getConfig().getBoolean("cross-server.enabled", false)) return;
    serverName =
        configManager.getConfig().getString("cross-server.server-name", DEFAULT_SERVER_NAME);
    channel = configManager.getConfig().getString("cross-server.channel", DEFAULT_CHANNEL);
    if (configManager.getConfig().getBoolean("cross-server.alerts.regular", true))
      mirroredTypes.add(SignalType.REGULAR);
    if (configManager.getConfig().getBoolean("cross-server.alerts.suspicious", true))
      mirroredTypes.add(SignalType.SUSPICIOUS);
    if (mirroredTypes.isEmpty()) {
      logger.info("[CrossServer] No alert types selected for mirroring, cross-server disabled.");
      return;
    }
    redisManager.start();
    if (!redisManager.isAvailable()) {
      logger.warning("[CrossServer] Redis unavailable; cross-server alerts disabled.");
      return;
    }
    enabled = true;
    redisManager.subscribe(channel, this::onMessage);
    alertManager.setCrossServerPublisher(this);
    logger.info(
        "[CrossServer] Enabled ("
            + serverName
            + "), mirroring: "
            + mirroredTypes
            + " on channel: "
            + channel);
  }

  @Override
  public void publish(SignalType type, Component component) {
    if (!enabled || !mirroredTypes.contains(type)) return;
    try {
      String componentJson = COMPONENT_SERIALIZER.serialize(component);
      CrossServerAlert alert = new CrossServerAlert(origin, serverName, type.name(), componentJson);
      String payload = mapper.writeValueAsString(alert);
      redisManager.publishAsync(channel, payload);
    } catch (Exception e) {
      logger.log(Level.FINE, "[CrossServer] Failed to publish alert", e);
    }
  }

  private void onMessage(String raw) {
    try {
      CrossServerAlert alert = mapper.readValue(raw, CrossServerAlert.class);
      if (alert.getOrigin().equals(origin)) return;
      SignalType type = SignalType.valueOf(alert.getType());
      if (!mirroredTypes.contains(type)) return;
      Component component = COMPONENT_SERIALIZER.deserialize(alert.getComponent());
      component = component.clickEvent(null);
      Component prefixed =
          MessageUtil.getMessage(Message.CROSS_SERVER_ALERT_PREFIX, "server", alert.getServer())
              .append(Component.space())
              .append(component);
      plugin.getServer().getScheduler().runTask(plugin, () -> alertManager.deliver(prefixed, type));
    } catch (Exception e) {
      logger.log(Level.FINE, "[CrossServer] Failed to process incoming alert", e);
    }
  }

  public void shutdown() {
    enabled = false;
    alertManager.setCrossServerPublisher(null);
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/alert/CrossServerPublisher.java ---

package club.nezxenka.netvision.service.bridge.alert;

import club.nezxenka.netvision.service.signal.model.SignalType;
import net.kyori.adventure.text.Component;

@FunctionalInterface
public interface CrossServerPublisher {
  void publish(SignalType type, Component component);
}


--- src/main/java/club/nezxenka/netvision/service/bridge/alert/model/CrossServerAlert.java ---

package club.nezxenka.netvision.service.bridge.alert.model;

import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonProperty;

public class CrossServerAlert {
  private final String origin;
  private final String server;
  private final String type;
  private final String component;

  @JsonCreator
  public CrossServerAlert(
      @JsonProperty("origin") String origin,
      @JsonProperty("server") String server,
      @JsonProperty("type") String type,
      @JsonProperty("component") String component) {
    this.origin = origin;
    this.server = server;
    this.type = type;
    this.component = component;
  }

  @JsonProperty("origin")
  public String getOrigin() {
    return origin;
  }

  @JsonProperty("server")
  public String getServer() {
    return server;
  }

  @JsonProperty("type")
  public String getType() {
    return type;
  }

  @JsonProperty("component")
  public String getComponent() {
    return component;
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/alert/model/serializer/AlertJsonSerializer.java ---

package club.nezxenka.netvision.service.bridge.alert.model.serializer;

import club.nezxenka.netvision.service.bridge.alert.model.CrossServerAlert;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

public class AlertJsonSerializer {
  private final ObjectMapper mapper;

  public AlertJsonSerializer(ObjectMapper mapper) {
    this.mapper = mapper;
  }

  public String serialize(CrossServerAlert alert) throws JsonProcessingException {
    return mapper.writeValueAsString(alert);
  }

  public CrossServerAlert deserialize(String json) throws JsonProcessingException {
    return mapper.readValue(json, CrossServerAlert.class);
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/alert/publisher/AlertMessagePublisher.java ---

package club.nezxenka.netvision.service.bridge.alert.publisher;

import club.nezxenka.netvision.service.bridge.alert.model.CrossServerAlert;
import club.nezxenka.netvision.service.bridge.connection.RedisManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.logging.Level;
import java.util.logging.Logger;
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.serializer.gson.GsonComponentSerializer;

public class AlertMessagePublisher {

  private static final GsonComponentSerializer SERIALIZER = GsonComponentSerializer.gson();
  private static final Logger LOGGER = Logger.getLogger(AlertMessagePublisher.class.getName());
  private final RedisManager redis;
  private final ObjectMapper mapper;
  private final String origin;
  private final String serverName;

  public AlertMessagePublisher(
      RedisManager redis, ObjectMapper mapper, String origin, String serverName) {
    this.redis = redis;
    this.mapper = mapper;
    this.origin = origin;
    this.serverName = serverName;
  }

  public void publish(SignalType type, Component component, String channel) {
    try {
      String json = SERIALIZER.serialize(component);
      CrossServerAlert alert = new CrossServerAlert(origin, serverName, type.name(), json);
      redis.publishAsync(channel, mapper.writeValueAsString(alert));
    } catch (Exception e) {
      LOGGER.log(Level.FINE, "Failed to publish cross-server alert.", e);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/alert/subscriber/AlertMessageSubscriber.java ---

package club.nezxenka.netvision.service.bridge.alert.subscriber;

import club.nezxenka.netvision.service.bridge.alert.model.CrossServerAlert;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.function.Consumer;
import java.util.logging.Level;
import java.util.logging.Logger;

public class AlertMessageSubscriber {

  private static final Logger LOGGER = Logger.getLogger(AlertMessageSubscriber.class.getName());
  private final ObjectMapper mapper;
  private final String origin;

  public AlertMessageSubscriber(ObjectMapper mapper, String origin) {
    this.mapper = mapper;
    this.origin = origin;
  }

  public void onMessage(String raw, Consumer<CrossServerAlert> handler) {
    try {
      CrossServerAlert alert = mapper.readValue(raw, CrossServerAlert.class);
      if (!alert.getOrigin().equals(origin)) handler.accept(alert);
    } catch (Exception e) {
      LOGGER.log(Level.FINE, "Failed to deserialize cross-server alert.", e);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/connection/factory/RedisClientFactory.java ---

package club.nezxenka.netvision.service.bridge.connection.factory;

import io.lettuce.core.ClientOptions;
import io.lettuce.core.RedisClient;
import io.lettuce.core.RedisURI;
import io.lettuce.core.SocketOptions;
import java.time.Duration;

public class RedisClientFactory {
  public RedisClient create(RedisURI uri, long timeoutSeconds) {
    RedisClient client = RedisClient.create(uri);
    client.setOptions(
        ClientOptions.builder()
            .disconnectedBehavior(ClientOptions.DisconnectedBehavior.REJECT_COMMANDS)
            .socketOptions(
                SocketOptions.builder().connectTimeout(Duration.ofSeconds(timeoutSeconds)).build())
            .build());
    return client;
  }

  public RedisURI.Builder uriBuilder(
      String host, int port, int database, boolean ssl, long timeoutSec) {
    return RedisURI.builder()
        .withHost(host)
        .withPort(port)
        .withDatabase(database)
        .withSsl(ssl)
        .withTimeout(Duration.ofSeconds(timeoutSec));
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/connection/RedisManager.java ---

package club.nezxenka.netvision.service.bridge.connection;

import club.nezxenka.netvision.core.config.ConfigManager;
import io.lettuce.core.ClientOptions;
import io.lettuce.core.KeyScanCursor;
import io.lettuce.core.RedisClient;
import io.lettuce.core.RedisURI;
import io.lettuce.core.ScanArgs;
import io.lettuce.core.SetArgs;
import io.lettuce.core.SocketOptions;
import io.lettuce.core.api.StatefulRedisConnection;
import io.lettuce.core.pubsub.RedisPubSubAdapter;
import io.lettuce.core.pubsub.StatefulRedisPubSubConnection;
import java.time.Duration;
import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;
import java.util.logging.Level;
import java.util.logging.Logger;

public class RedisManager {

  private static final String DEFAULT_HOST = "localhost";
  private static final int DEFAULT_PORT = 6379;
  private static final int DEFAULT_DATABASE = 0;
  private static final long DEFAULT_TIMEOUT_SECONDS = 10L;
  private static final long SCAN_BATCH = 256L;
  private static final Duration SHUTDOWN_TIMEOUT = Duration.ofSeconds(2);
  private final ConfigManager configManager;
  private final Logger logger;
  private boolean attempted = false;
  private RedisClient client;
  private StatefulRedisConnection<String, String> connection;
  private StatefulRedisPubSubConnection<String, String> pubSubConnection;
  private volatile boolean available = false;

  public RedisManager(ConfigManager configManager, Logger logger) {
    this.configManager = configManager;
    this.logger = logger;
  }

  public boolean isAvailable() {
    return available;
  }

  public void start() {
    if (attempted) return;
    attempted = true;
    if (!configManager.getConfig().getBoolean("redis.enabled", false)) return;
    String host = configManager.getConfig().getString("redis.host", DEFAULT_HOST);
    int port = configManager.getConfig().getInt("redis.port", DEFAULT_PORT);
    int database = configManager.getConfig().getInt("redis.database", DEFAULT_DATABASE);
    boolean useSsl = configManager.getConfig().getBoolean("redis.ssl", false);
    long timeoutSeconds =
        Math.max(
            1L,
            configManager.getConfig().getLong("redis.timeout-seconds", DEFAULT_TIMEOUT_SECONDS));
    String password = configManager.getConfig().getString("redis.password", "");
    RedisURI.Builder uriBuilder =
        RedisURI.builder()
            .withHost(host)
            .withPort(port)
            .withDatabase(database)
            .withSsl(useSsl)
            .withTimeout(Duration.ofSeconds(timeoutSeconds));
    if (!password.isEmpty()) uriBuilder.withPassword(password.toCharArray());
    try {
      RedisClient redisClient = RedisClient.create(uriBuilder.build());
      redisClient.setOptions(
          ClientOptions.builder()
              .disconnectedBehavior(ClientOptions.DisconnectedBehavior.REJECT_COMMANDS)
              .socketOptions(
                  SocketOptions.builder()
                      .connectTimeout(Duration.ofSeconds(timeoutSeconds))
                      .build())
              .build());
      this.client = redisClient;
      this.connection = redisClient.connect();
      this.pubSubConnection = redisClient.connectPubSub();
      this.available = true;
      logger.info("[Redis] Connected to " + host + ":" + port + " (database " + database + ").");
    } catch (Exception e) {
      logger.log(
          Level.WARNING,
          "[Redis] Could not connect to "
              + host
              + ":"
              + port
              + "; cross-server features are disabled.",
          e);
      shutdown();
    }
  }

  public void publishAsync(String channel, String message) {
    if (!available || connection == null) return;
    try {
      connection
          .async()
          .publish(channel, message)
          .exceptionally(
              error -> {
                logger.log(Level.FINE, "[Redis] Publish to " + channel + " failed.", error);
                return 0L;
              });
    } catch (Exception e) {
      logger.log(Level.FINE, "[Redis] Publish to " + channel + " failed.", e);
    }
  }

  public void setWithTtl(String key, String value, long ttlSeconds) {
    if (!available || connection == null) return;
    try {
      connection
          .async()
          .set(key, value, SetArgs.Builder.ex(ttlSeconds))
          .exceptionally(
              error -> {
                logger.log(Level.FINE, "[Redis] Set " + key + " failed.", error);
                return null;
              });
    } catch (Exception e) {
      logger.log(Level.FINE, "[Redis] Set " + key + " failed.", e);
    }
  }

  public List<String> scanValues(String pattern) {
    if (!available || connection == null) return List.of();
    try {
      var commands = connection.sync();
      ScanArgs args = ScanArgs.Builder.matches(pattern).limit(SCAN_BATCH);
      List<String> keys = new ArrayList<>();
      KeyScanCursor<String> cursor = commands.scan(args);
      keys.addAll(cursor.getKeys());
      while (!cursor.isFinished()) {
        cursor = commands.scan(cursor, args);
        keys.addAll(cursor.getKeys());
      }
      if (keys.isEmpty()) return List.of();
      return commands.mget(keys.toArray(new String[0])).stream()
          .map(kv -> kv.hasValue() ? kv.getValue() : null)
          .filter(v -> v != null)
          .toList();
    } catch (Exception e) {
      logger.log(Level.FINE, "[Redis] Scan " + pattern + " failed.", e);
      return List.of();
    }
  }

  public void subscribe(String channel, Consumer<String> onMessage) {
    if (pubSubConnection == null) return;
    pubSubConnection.addListener(
        new RedisPubSubAdapter<>() {
          @Override
          public void message(String receivedChannel, String message) {
            if (channel.equals(receivedChannel)) onMessage.accept(message);
          }
        });
    pubSubConnection.sync().subscribe(channel);
  }

  public void shutdown() {
    available = false;
    attempted = false;
    try {
      if (connection != null) connection.close();
    } catch (Exception e) {
      logger.log(Level.FINE, "[Redis] Error closing connection.", e);
    }
    try {
      if (pubSubConnection != null) pubSubConnection.close();
    } catch (Exception e) {
      logger.log(Level.FINE, "[Redis] Error closing pubSubConnection.", e);
    }
    try {
      if (client != null) client.shutdown(Duration.ZERO, SHUTDOWN_TIMEOUT);
    } catch (Exception e) {
      logger.log(Level.FINE, "[Redis] Error shutting down client.", e);
    }
    connection = null;
    pubSubConnection = null;
    client = null;
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/connection/subscriber/ChannelSubscriber.java ---

package club.nezxenka.netvision.service.bridge.connection.subscriber;

import io.lettuce.core.pubsub.RedisPubSubAdapter;
import io.lettuce.core.pubsub.StatefulRedisPubSubConnection;
import java.util.function.Consumer;

public class ChannelSubscriber {
  public void subscribe(
      StatefulRedisPubSubConnection<String, String> conn,
      String channel,
      Consumer<String> handler) {
    conn.addListener(
        new RedisPubSubAdapter<>() {
          @Override
          public void message(String ch, String msg) {
            if (channel.equals(ch)) handler.accept(msg);
          }
        });
    conn.sync().subscribe(channel);
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/suspicious/CrossServerSuspiciousService.java ---

package club.nezxenka.netvision.service.bridge.suspicious;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.service.bridge.connection.RedisManager;
import club.nezxenka.netvision.service.bridge.suspicious.model.SuspiciousSnapshot;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.List;
import java.util.logging.Level;
import java.util.logging.Logger;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;

public class CrossServerSuspiciousService {
  private static final String DEFAULT_SERVER_NAME = "server-1";
  private static final String DEFAULT_CHANNEL = "netvision:alerts";
  private static final long DEFAULT_TTL_SECONDS = 30L;
  private static final long DEFAULT_REFRESH_SECONDS = 10L;
  private final ConfigManager configManager;
  private final RedisManager redisManager;
  private final PlayerDataManager playerDataManager;
  private final NetVision plugin;
  private final Logger logger;
  private final ObjectMapper mapper = new ObjectMapper();
  private boolean enabled = false;
  private String serverName = DEFAULT_SERVER_NAME;
  private String keyPrefix = DEFAULT_CHANNEL + ":suspect";
  private long ttlSeconds = DEFAULT_TTL_SECONDS;
  private int taskId = -1;

  public CrossServerSuspiciousService(
      ConfigManager configManager,
      RedisManager redisManager,
      PlayerDataManager playerDataManager,
      NetVision plugin,
      Logger logger) {
    this.configManager = configManager;
    this.redisManager = redisManager;
    this.playerDataManager = playerDataManager;
    this.plugin = plugin;
    this.logger = logger;
  }

  public boolean isActive() {
    return enabled;
  }

  public void start() {
    if (!configManager.getConfig().getBoolean("cross-server.enabled", false)
        || !configManager.getConfig().getBoolean("cross-server.alerts.suspicious", true)) return;
    serverName =
        configManager.getConfig().getString("cross-server.server-name", DEFAULT_SERVER_NAME);
    String channel = configManager.getConfig().getString("cross-server.channel", DEFAULT_CHANNEL);
    keyPrefix = channel + ":suspect";
    ttlSeconds =
        configManager
            .getConfig()
            .getLong("cross-server.suspicious-sync.ttl-seconds", DEFAULT_TTL_SECONDS);
    long refreshSeconds =
        Math.max(
            1L,
            Math.min(
                configManager
                    .getConfig()
                    .getLong(
                        "cross-server.suspicious-sync.refresh-seconds", DEFAULT_REFRESH_SECONDS),
                ttlSeconds));
    ttlSeconds = Math.max(ttlSeconds, refreshSeconds + 1L);
    if (!redisManager.isAvailable()) {
      logger.warning(
          "[CrossServer] suspicious-sync enabled but Redis unavailable; list stays local.");
      return;
    }
    enabled = true;
    long periodTicks = refreshSeconds * 20L;
    taskId =
        Bukkit.getScheduler()
            .runTaskTimerAsynchronously(
                plugin, this::publishLocalSuspicious, periodTicks, periodTicks)
            .getTaskId();
    logger.info(
        "[CrossServer] Sharing suspicious players as \""
            + serverName
            + "\" (refresh "
            + refreshSeconds
            + "s, ttl "
            + ttlSeconds
            + "s).");
  }

  private void publishLocalSuspicious() {
    if (!enabled) return;
    for (NetVisionPlayer nvPlayer : playerDataManager.getPlayers()) publishPlayer(nvPlayer);
  }

  private void publishPlayer(NetVisionPlayer nvPlayer) {
    NeuralAnalyzer check = nvPlayer.getModuleCoordinator().getModule(NeuralAnalyzer.class);
    if (check == null || check.getBuffer() <= 0.0) return;
    Player player = nvPlayer.getPlayer();
    try {
      SuspiciousSnapshot snapshot =
          new SuspiciousSnapshot(
              serverName,
              nvPlayer.getUuid().toString(),
              player.getName(),
              check.getBuffer(),
              player.getPing(),
              System.currentTimeMillis());
      String payload = mapper.writeValueAsString(snapshot);
      redisManager.setWithTtl(
          keyPrefix + ":" + serverName + ":" + nvPlayer.getUuid(), payload, ttlSeconds);
    } catch (Exception e) {
      logger.log(Level.FINE, "[CrossServer] Failed to publish suspect " + player.getName(), e);
    }
  }

  public List<SuspiciousSnapshot> fetchRemote() {
    if (!enabled) return List.of();
    return redisManager.scanValues(keyPrefix + ":*").stream()
        .map(
            raw -> {
              try {
                return mapper.readValue(raw, SuspiciousSnapshot.class);
              } catch (Exception e) {
                logger.log(Level.FINE, "[CrossServer] Bad suspect payload.", e);
                return null;
              }
            })
        .filter(s -> s != null && !s.getServer().equals(serverName))
        .toList();
  }

  public void shutdown() {
    enabled = false;
    if (taskId != -1) {
      Bukkit.getScheduler().cancelTask(taskId);
      taskId = -1;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/suspicious/fetcher/RemoteSuspiciousFetcher.java ---

package club.nezxenka.netvision.service.bridge.suspicious.fetcher;

import club.nezxenka.netvision.service.bridge.connection.RedisManager;
import club.nezxenka.netvision.service.bridge.suspicious.model.SuspiciousSnapshot;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.List;

public class RemoteSuspiciousFetcher {
  private final RedisManager redis;
  private final ObjectMapper mapper;
  private final String serverName;

  public RemoteSuspiciousFetcher(RedisManager redis, ObjectMapper mapper, String serverName) {
    this.redis = redis;
    this.mapper = mapper;
    this.serverName = serverName;
  }

  public List<SuspiciousSnapshot> fetch(String keyPrefix) {
    return redis.scanValues(keyPrefix + ":*").stream()
        .map(
            raw -> {
              try {
                return mapper.readValue(raw, SuspiciousSnapshot.class);
              } catch (Exception e) {
                return null;
              }
            })
        .filter(s -> s != null && !s.getServer().equals(serverName))
        .toList();
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/suspicious/model/dto/SuspiciousEntryDTO.java ---

package club.nezxenka.netvision.service.bridge.suspicious.model.dto;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class SuspiciousEntryDTO {
  private String playerName;
  private String serverName;
  private double buffer;
  private int ping;
  private long lastUpdate;
}


--- src/main/java/club/nezxenka/netvision/service/bridge/suspicious/model/SuspiciousSnapshot.java ---

package club.nezxenka.netvision.service.bridge.suspicious.model;

import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonProperty;

public class SuspiciousSnapshot {
  private final String server;
  private final String uuid;
  private final String name;
  private final double buffer;
  private final int ping;
  private final long updatedAt;

  @JsonCreator
  public SuspiciousSnapshot(
      @JsonProperty("server") String server,
      @JsonProperty("uuid") String uuid,
      @JsonProperty("name") String name,
      @JsonProperty("buffer") double buffer,
      @JsonProperty("ping") int ping,
      @JsonProperty("updatedAt") long updatedAt) {
    this.server = server;
    this.uuid = uuid;
    this.name = name;
    this.buffer = buffer;
    this.ping = ping;
    this.updatedAt = updatedAt;
  }

  @JsonProperty("server")
  public String getServer() {
    return server;
  }

  @JsonProperty("uuid")
  public String getUuid() {
    return uuid;
  }

  @JsonProperty("name")
  public String getName() {
    return name;
  }

  @JsonProperty("buffer")
  public double getBuffer() {
    return buffer;
  }

  @JsonProperty("ping")
  public int getPing() {
    return ping;
  }

  @JsonProperty("updatedAt")
  public long getUpdatedAt() {
    return updatedAt;
  }
}


--- src/main/java/club/nezxenka/netvision/service/bridge/suspicious/publisher/SuspiciousSnapshotPublisher.java ---

package club.nezxenka.netvision.service.bridge.suspicious.publisher;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.service.bridge.connection.RedisManager;
import club.nezxenka.netvision.service.bridge.suspicious.model.SuspiciousSnapshot;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.logging.Level;
import java.util.logging.Logger;
import org.bukkit.entity.Player;

public class SuspiciousSnapshotPublisher {

  private static final Logger LOGGER =
      Logger.getLogger(SuspiciousSnapshotPublisher.class.getName());
  private final RedisManager redis;
  private final ObjectMapper mapper;
  private final String serverName;

  public SuspiciousSnapshotPublisher(RedisManager redis, ObjectMapper mapper, String serverName) {
    this.redis = redis;
    this.mapper = mapper;
    this.serverName = serverName;
  }

  public void publish(NetVisionPlayer nvPlayer, NeuralAnalyzer check, String keyPrefix, long ttl) {
    Player player = nvPlayer.getPlayer();
    try {
      SuspiciousSnapshot snap =
          new SuspiciousSnapshot(
              serverName,
              nvPlayer.getUuid().toString(),
              player.getName(),
              check.getBuffer(),
              player.getPing(),
              System.currentTimeMillis());
      redis.setWithTtl(
          keyPrefix + ":" + serverName + ":" + nvPlayer.getUuid(),
          mapper.writeValueAsString(snap),
          ttl);
    } catch (Exception e) {
      LOGGER.log(Level.FINE, "Failed to publish suspicious snapshot.", e);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/api/executor/CommandExecutorContext.java ---

package club.nezxenka.netvision.service.command.api.executor;

import club.nezxenka.netvision.audience.api.Sender;
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class CommandExecutorContext {
  private Sender sender;
  private String rootName;
  private String subCommand;
}


--- src/main/java/club/nezxenka/netvision/service/command/api/NetVisionCommand.java ---

package club.nezxenka.netvision.service.command.api;

import club.nezxenka.netvision.audience.api.Sender;
import org.incendo.cloud.CommandManager;

public interface NetVisionCommand {
  void register(CommandManager<Sender> manager, String rootName);
}


--- src/main/java/club/nezxenka/netvision/service/command/api/registry/CommandRegistryEntry.java ---

package club.nezxenka.netvision.service.command.api.registry;

import club.nezxenka.netvision.service.command.api.NetVisionCommand;

public record CommandRegistryEntry(String name, NetVisionCommand command, boolean playerOnly) {}


--- src/main/java/club/nezxenka/netvision/service/command/api/SenderRequirement.java ---

package club.nezxenka.netvision.service.command.api;

import club.nezxenka.netvision.audience.api.Sender;
import net.kyori.adventure.text.Component;
import org.checkerframework.checker.nullness.qual.NonNull;
import org.incendo.cloud.processors.requirements.Requirement;

public interface SenderRequirement extends Requirement<Sender, SenderRequirement> {
  @NonNull Component errorMessage(Sender sender);
}


--- src/main/java/club/nezxenka/netvision/service/command/failure/CommandFailureHandler.java ---

package club.nezxenka.netvision.service.command.failure;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.SenderRequirement;
import org.checkerframework.checker.nullness.qual.NonNull;
import org.incendo.cloud.context.CommandContext;
import org.incendo.cloud.processors.requirements.RequirementFailureHandler;

public class CommandFailureHandler implements RequirementFailureHandler<Sender, SenderRequirement> {
  @Override
  public void handleFailure(
      @NonNull CommandContext<Sender> context, @NonNull SenderRequirement requirement) {
    context.sender().sendMessage(requirement.errorMessage(context.sender()));
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/failure/renderer/ErrorMessageRenderer.java ---

package club.nezxenka.netvision.service.command.failure.renderer;

import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import net.kyori.adventure.text.Component;

public class ErrorMessageRenderer {
  public Component playerOnly() {
    return MessageUtil.getMessage(Message.RUN_AS_PLAYER);
  }

  public Component playerNotFound() {
    return MessageUtil.getMessage(Message.PLAYER_NOT_FOUND);
  }

  public Component internalError() {
    return MessageUtil.getMessage(Message.INTERNAL_ERROR);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/framework/bootstrap/CloudBootstrapService.java ---

package club.nezxenka.netvision.service.command.framework.bootstrap;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.audience.factory.SenderFactory;
import org.incendo.cloud.bukkit.CloudBukkitCapabilities;
import org.incendo.cloud.execution.ExecutionCoordinator;
import org.incendo.cloud.paper.LegacyPaperCommandManager;

public class CloudBootstrapService {
  public LegacyPaperCommandManager<Sender> bootstrap(NetVision plugin) {
    SenderFactory senderFactory = new SenderFactory(plugin);
    LegacyPaperCommandManager<Sender> manager;
    try {
      manager =
          new LegacyPaperCommandManager<>(
              plugin, ExecutionCoordinator.simpleCoordinator(), senderFactory);
    } catch (Exception e) {
      plugin.getLogger().severe("Failed to initialize Cloud Command Manager: " + e.getMessage());
      return null;
    }
    if (manager.hasCapability(CloudBukkitCapabilities.NATIVE_BRIGADIER))
      manager.registerBrigadier();
    else if (manager.hasCapability(CloudBukkitCapabilities.ASYNCHRONOUS_COMPLETION))
      manager.registerAsynchronousCompletions();
    return manager;
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/framework/CommandFramework.java ---

package club.nezxenka.netvision.service.command.framework;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.audience.factory.SenderFactory;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import org.incendo.cloud.bukkit.CloudBukkitCapabilities;
import org.incendo.cloud.execution.ExecutionCoordinator;
import org.incendo.cloud.paper.LegacyPaperCommandManager;

public class CommandFramework {
  public CommandFramework(
      NetVision plugin,
      SignalManager alertManager,
      DatabaseManager databaseManager,
      ConfigManager configManager,
      LocaleManager localeManager,
      PlayerDataManager playerDataManager,
      HistoryMenu historyMenu) {
    LegacyPaperCommandManager<Sender> cloudManager = setupCloud(plugin);
    if (cloudManager != null)
      CommandRegistrationService.registerCommands(
          cloudManager,
          plugin,
          alertManager,
          databaseManager,
          configManager,
          localeManager,
          playerDataManager,
          historyMenu);
  }

  private LegacyPaperCommandManager<Sender> setupCloud(NetVision plugin) {
    SenderFactory senderFactory = new SenderFactory(plugin);
    LegacyPaperCommandManager<Sender> manager;
    try {
      manager =
          new LegacyPaperCommandManager<>(
              plugin, ExecutionCoordinator.simpleCoordinator(), senderFactory);
    } catch (Exception e) {
      plugin.getLogger().severe("Failed to initialize Cloud Command Manager: " + e.getMessage());
      e.printStackTrace();
      return null;
    }
    if (manager.hasCapability(CloudBukkitCapabilities.NATIVE_BRIGADIER))
      manager.registerBrigadier();
    else if (manager.hasCapability(CloudBukkitCapabilities.ASYNCHRONOUS_COMPLETION))
      manager.registerAsynchronousCompletions();
    return manager;
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/framework/CommandRegistrationService.java ---

package club.nezxenka.netvision.service.command.framework;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.service.command.api.SenderRequirement;
import club.nezxenka.netvision.service.command.failure.CommandFailureHandler;
import club.nezxenka.netvision.service.command.impl.alerts.AlertsCommand;
import club.nezxenka.netvision.service.command.impl.ban.NvpBanCommand;
import club.nezxenka.netvision.service.command.impl.brands.BrandsCommand;
import club.nezxenka.netvision.service.command.impl.falsepositive.FalsePositiveCommand;
import club.nezxenka.netvision.service.command.impl.help.HelpCommand;
import club.nezxenka.netvision.service.command.impl.history.HistoryCommand;
import club.nezxenka.netvision.service.command.impl.logs.LogsCommand;
import club.nezxenka.netvision.service.command.impl.menu.MenuCommand;
import club.nezxenka.netvision.service.command.impl.prob.ProbCommand;
import club.nezxenka.netvision.service.command.impl.profile.ProfileCommand;
import club.nezxenka.netvision.service.command.impl.punish.PunishCommand;
import club.nezxenka.netvision.service.command.impl.reload.ReloadCommand;
import club.nezxenka.netvision.service.command.impl.stats.StatsCommand;
import club.nezxenka.netvision.service.command.impl.status.StatusCommand;
import club.nezxenka.netvision.service.command.impl.suspicious.SuspiciousCommand;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.util.message.MessageUtil;
import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import io.leangen.geantyref.TypeToken;
import java.util.function.Function;
import net.kyori.adventure.text.ComponentLike;
import net.kyori.adventure.text.format.NamedTextColor;
import org.incendo.cloud.exception.InvalidSyntaxException;
import org.incendo.cloud.key.CloudKey;
import org.incendo.cloud.processors.requirements.RequirementApplicable;
import org.incendo.cloud.processors.requirements.RequirementPostprocessor;

public class CommandRegistrationService {

  public static final CloudKey<
          org.incendo.cloud.processors.requirements.Requirements<Sender, SenderRequirement>>
      REQUIREMENT_KEY = CloudKey.of("netvision_requirements", new TypeToken<>() {});
  public static final RequirementApplicable.RequirementApplicableFactory<Sender, SenderRequirement>
      REQUIREMENT_FACTORY = RequirementApplicable.factory(REQUIREMENT_KEY);
  private static boolean commandsRegistered = false;

  public static void registerCommands(
      org.incendo.cloud.CommandManager<Sender> commandManager,
      NetVision plugin,
      SignalManager alertManager,
      DatabaseManager databaseManager,
      ConfigManager configManager,
      LocaleManager localeManager,
      PlayerDataManager playerDataManager,
      HistoryMenu historyMenu) {
    if (commandsRegistered) return;
    for (String root : new String[] {"netvision", "nvp"}) {
      new HelpCommand().register(commandManager, root);
      new AlertsCommand(alertManager).register(commandManager, root);
      new ReloadCommand(plugin).register(commandManager, root);
      new ProbCommand(playerDataManager, localeManager, plugin).register(commandManager, root);
      new ProfileCommand(playerDataManager, localeManager).register(commandManager, root);
      new HistoryCommand(historyMenu).register(commandManager, root);
      new LogsCommand(plugin, databaseManager, configManager, localeManager)
          .register(commandManager, root);
      new PunishCommand(databaseManager).register(commandManager, root);
      new BrandsCommand(alertManager).register(commandManager, root);
      new SuspiciousCommand(playerDataManager, alertManager).register(commandManager, root);
      new StatsCommand(plugin, databaseManager, playerDataManager).register(commandManager, root);
      new MenuCommand(plugin.getChickenCoopMenu()).register(commandManager, root);
      new FalsePositiveCommand(plugin, playerDataManager).register(commandManager, root);
      new StatusCommand(plugin.getHologramManager()).register(commandManager, root);
    }
    new NvpBanCommand(plugin).register(commandManager, "nvp");
    final RequirementPostprocessor<Sender, SenderRequirement> senderRequirementPostprocessor =
        RequirementPostprocessor.of(REQUIREMENT_KEY, new CommandFailureHandler());
    commandManager.registerCommandPostProcessor(senderRequirementPostprocessor);
    registerExceptionHandler(
        commandManager, InvalidSyntaxException.class, e -> MessageUtil.format(e.correctSyntax()));
    commandsRegistered = true;
  }

  private static <E extends Exception> void registerExceptionHandler(
      org.incendo.cloud.CommandManager<Sender> commandManager,
      Class<E> ex,
      Function<E, ComponentLike> toComponent) {
    commandManager
        .exceptionController()
        .registerHandler(
            ex,
            c ->
                c.context()
                    .sender()
                    .sendMessage(
                        toComponent
                            .apply(c.exception())
                            .asComponent()
                            .colorIfAbsent(NamedTextColor.RED)));
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/framework/handler/ExceptionHandlerRegistry.java ---

package club.nezxenka.netvision.service.command.framework.handler;

import club.nezxenka.netvision.audience.api.Sender;
import java.util.function.Function;
import net.kyori.adventure.text.ComponentLike;
import net.kyori.adventure.text.format.NamedTextColor;
import org.incendo.cloud.CommandManager;

public class ExceptionHandlerRegistry {
  public <E extends Exception> void register(
      CommandManager<Sender> manager, Class<E> exceptionType, Function<E, ComponentLike> mapper) {
    manager
        .exceptionController()
        .registerHandler(
            exceptionType,
            ctx ->
                ctx.context()
                    .sender()
                    .sendMessage(
                        mapper
                            .apply(ctx.exception())
                            .asComponent()
                            .colorIfAbsent(NamedTextColor.RED)));
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/alerts/AlertsCommand.java ---

package club.nezxenka.netvision.service.command.impl.alerts;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class AlertsCommand implements NetVisionCommand {
  private final SignalManager alertManager;

  public AlertsCommand(SignalManager alertManager) {
    this.alertManager = alertManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("alerts")
            .permission("netvision.alerts")
            .handler(this::execute));
  }

  private void execute(CommandContext<Sender> context) {
    CommandSender nativeSender = context.sender().getNativeSender();
    if (nativeSender instanceof Player player)
      alertManager.toggle(player, SignalType.REGULAR, false);
    else {
      alertManager.toggleConsoleAlerts(SignalType.REGULAR);
      if (alertManager.isConsoleAlertsEnabled(SignalType.REGULAR))
        MessageUtil.sendMessage(nativeSender, Message.ALERTS_ENABLED);
      else MessageUtil.sendMessage(nativeSender, Message.ALERTS_DISABLED);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/alerts/handler/AlertToggleExecutor.java ---

package club.nezxenka.netvision.service.command.impl.alerts.handler;

import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;

public class AlertToggleExecutor {
  private final SignalManager alertManager;

  public AlertToggleExecutor(SignalManager alertManager) {
    this.alertManager = alertManager;
  }

  public void execute(CommandSender sender, SignalType type) {
    if (sender instanceof Player player) alertManager.toggle(player, type, false);
    else {
      alertManager.toggleConsoleAlerts(type);
      boolean enabled = alertManager.isConsoleAlertsEnabled(type);
      MessageUtil.sendMessage(sender, enabled ? Message.ALERTS_ENABLED : Message.ALERTS_DISABLED);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/ban/executor/BanCommandExecutor.java ---

package club.nezxenka.netvision.service.command.impl.ban.executor;

import org.bukkit.Bukkit;

public class BanCommandExecutor {
  public void execute(String targetName) {
    Bukkit.dispatchCommand(Bukkit.getConsoleSender(), "shame ban " + targetName);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/ban/NvpBanCommand.java ---

package club.nezxenka.netvision.service.command.impl.ban;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class NvpBanCommand implements NetVisionCommand {

  public NvpBanCommand(NetVision plugin) {}

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("ban")
            .permission("netvision.ban")
            .required("target", org.incendo.cloud.bukkit.parser.PlayerParser.playerParser())
            .handler(this::ban));
  }

  private void ban(CommandContext<Sender> context) {
    Player target = context.get("target");
    String targetName = target.getName();
    Bukkit.dispatchCommand(Bukkit.getConsoleSender(), "shame ban " + targetName);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/brands/BrandsCommand.java ---

package club.nezxenka.netvision.service.command.impl.brands;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class BrandsCommand implements NetVisionCommand {
  private final SignalManager alertManager;

  public BrandsCommand(SignalManager alertManager) {
    this.alertManager = alertManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("brands")
            .permission("netvision.brand")
            .handler(this::execute));
  }

  private void execute(CommandContext<Sender> context) {
    CommandSender nativeSender = context.sender().getNativeSender();
    if (nativeSender instanceof Player player) alertManager.toggle(player, SignalType.BRAND, false);
    else {
      alertManager.toggleConsoleAlerts(SignalType.BRAND);
      if (alertManager.isConsoleAlertsEnabled(SignalType.BRAND))
        MessageUtil.sendMessage(nativeSender, Message.BRAND_ALERTS_ENABLED);
      else MessageUtil.sendMessage(nativeSender, Message.BRAND_ALERTS_DISABLED);
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/brands/handler/BrandToggleExecutor.java ---

package club.nezxenka.netvision.service.command.impl.brands.handler;

import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;

public class BrandToggleExecutor {
  private final SignalManager alertManager;

  public BrandToggleExecutor(SignalManager alertManager) {
    this.alertManager = alertManager;
  }

  public void toggle(CommandSender sender) {
    if (sender instanceof Player player) alertManager.toggle(player, SignalType.BRAND, false);
    else alertManager.toggleConsoleAlerts(SignalType.BRAND);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/falsepositive/FalsePositiveCommand.java ---

package club.nezxenka.netvision.service.command.impl.falsepositive;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.engine.model.TickSample;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.remote.restore.DataRestorer;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class FalsePositiveCommand implements NetVisionCommand {

  private final PlayerDataManager playerDataManager;
  private final DataRestorer dataRestorer;

  public FalsePositiveCommand(NetVision plugin, PlayerDataManager playerDataManager) {
    this.playerDataManager = playerDataManager;
    this.dataRestorer = new DataRestorer(plugin);
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("falsepositive", "fp")
            .permission("netvision.falsepositive")
            .literal("restore")
            .required("target", org.incendo.cloud.bukkit.parser.PlayerParser.playerParser())
            .handler(this::handleFalsePositive));
  }

  private void handleFalsePositive(CommandContext<Sender> context) {
    Sender sender = context.sender();
    Player target = context.get("target");
    NetVisionPlayer nvPlayer = playerDataManager.getPlayer(target);
    if (nvPlayer == null) {
      MessageUtil.sendMessage(sender.getNativeSender(), Message.FP_NO_DATA);
      return;
    }
    NeuralAnalyzer aiCheck = nvPlayer.getModuleCoordinator().getModule(NeuralAnalyzer.class);
    if (aiCheck == null) {
      MessageUtil.sendMessage(sender.getNativeSender(), Message.FP_NO_DATA);
      return;
    }
    java.util.List<TickSample> history = aiCheck.getTickHistory();
    if (history.isEmpty()) {
      MessageUtil.sendMessage(sender.getNativeSender(), Message.FP_NO_DATA);
      return;
    }
    boolean success = dataRestorer.restoreData(target.getName(), history);
    if (success)
      MessageUtil.sendMessage(
          sender.getNativeSender(), Message.FP_SUCCESS, "player", target.getName());
    else MessageUtil.sendMessage(sender.getNativeSender(), Message.FP_FAIL);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/falsepositive/restore/TickDataRestoreService.java ---

package club.nezxenka.netvision.service.command.impl.falsepositive.restore;

import club.nezxenka.netvision.engine.model.TickSample;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.remote.restore.DataRestorer;
import java.util.List;

public class TickDataRestoreService {
  private final DataRestorer restorer;

  public TickDataRestoreService(DataRestorer restorer) {
    this.restorer = restorer;
  }

  public boolean restore(String playerName, NeuralAnalyzer check) {
    List<TickSample> history = check.getTickHistory();
    if (history == null || history.isEmpty()) return false;
    return restorer.restoreData(playerName, history);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/help/HelpCommand.java ---

package club.nezxenka.netvision.service.command.impl.help;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class HelpCommand implements NetVisionCommand {
  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    final var builder = manager.commandBuilder(rootName).permission("netvision.help");
    manager.command(builder.handler(this::help));
    manager.command(builder.literal("help").handler(this::help));
  }

  private void help(CommandContext<Sender> context) {
    final Sender sender = context.sender();
    MessageUtil.sendMessageList(sender.getNativeSender(), Message.HELP_MESSAGE);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/help/renderer/HelpMessageComposer.java ---

package club.nezxenka.netvision.service.command.impl.help.renderer;

import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.List;
import net.kyori.adventure.text.Component;

public class HelpMessageComposer {
  public List<Component> compose() {
    return MessageUtil.getMessageList(Message.HELP_MESSAGE);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/history/HistoryCommand.java ---

package club.nezxenka.netvision.service.command.impl.history;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.service.command.framework.CommandRegistrationService;
import club.nezxenka.netvision.service.command.requirement.PlayerSenderRequirement;
import club.nezxenka.netvision.visual.menu.history.HistoryMenu;
import org.bukkit.OfflinePlayer;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.bukkit.parser.OfflinePlayerParser;
import org.incendo.cloud.context.CommandContext;

public class HistoryCommand implements NetVisionCommand {
  private final HistoryMenu historyMenu;

  public HistoryCommand(HistoryMenu historyMenu) {
    this.historyMenu = historyMenu;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("history", "hist")
            .permission("netvision.history")
            .required("target", OfflinePlayerParser.offlinePlayerParser())
            .apply(
                CommandRegistrationService.REQUIREMENT_FACTORY.create(
                    PlayerSenderRequirement.PLAYER_SENDER_REQUIREMENT))
            .handler(this::handleHistory));
  }

  private void handleHistory(CommandContext<Sender> context) {
    Player viewer = context.sender().getPlayer();
    OfflinePlayer target = context.get("target");
    historyMenu.open(viewer, target.getName(), target.getUniqueId(), 1);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/history/session/HistorySessionManager.java ---

package club.nezxenka.netvision.service.command.impl.history.session;

import club.nezxenka.netvision.visual.menu.history.model.HistorySession;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class HistorySessionManager {
  private final Map<UUID, HistorySession> sessions = new ConcurrentHashMap<>();

  public void register(UUID viewer, HistorySession session) {
    sessions.put(viewer, session);
  }

  public HistorySession get(UUID viewer) {
    return sessions.get(viewer);
  }

  public void remove(UUID viewer) {
    sessions.remove(viewer);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/logs/LogsCommand.java ---

package club.nezxenka.netvision.service.command.impl.logs;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.core.storage.model.Infraction;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import club.nezxenka.netvision.util.time.TimeUtil;
import java.util.List;
import java.util.concurrent.TimeUnit;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;
import org.incendo.cloud.parser.standard.IntegerParser;
import org.incendo.cloud.parser.standard.StringParser;

public class LogsCommand implements NetVisionCommand {

  private final NetVision plugin;
  private final DatabaseManager databaseManager;
  private final ConfigManager configManager;
  private final LocaleManager localeManager;

  public LogsCommand(
      NetVision plugin,
      DatabaseManager databaseManager,
      ConfigManager configManager,
      LocaleManager localeManager) {
    this.plugin = plugin;
    this.databaseManager = databaseManager;
    this.configManager = configManager;
    this.localeManager = localeManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("logs")
            .permission("netvision.logs")
            .optional("page", IntegerParser.integerParser(1))
            .flag(manager.flagBuilder("time").withComponent(StringParser.stringParser()))
            .handler(this::handleLogs));
  }

  private void handleLogs(CommandContext<Sender> context) {
    Sender sender = context.sender();
    int page = context.getOrDefault("page", 1);
    String timeArg = context.flags().get("time");
    if (databaseManager.getDatabase() == null
        || !configManager.getConfig().getBoolean("history.enabled", false)) {
      MessageUtil.sendMessage(sender.getNativeSender(), Message.HISTORY_DISABLED);
      return;
    }
    long since = parseTime(timeArg);
    if (since == -1L) {
      MessageUtil.sendMessage(sender.getNativeSender(), Message.LOGS_INVALID_TIME);
      return;
    }
    plugin
        .getServer()
        .getScheduler()
        .runTaskAsynchronously(
            plugin,
            () -> {
              int entriesPerPage = 10;
              List<Infraction> violations =
                  databaseManager.getDatabase().getViolations(page, entriesPerPage, since);
              int totalLogs = databaseManager.getDatabase().getLogCount(since);
              int maxPages = Math.max(1, (int) Math.ceil((double) totalLogs / entriesPerPage));
              MessageUtil.sendMessage(
                  sender.getNativeSender(),
                  Message.LOGS_HEADER,
                  "page",
                  String.valueOf(page),
                  "max_pages",
                  String.valueOf(maxPages));
              if (violations.isEmpty()) {
                MessageUtil.sendMessage(sender.getNativeSender(), Message.LOGS_NO_VIOLATIONS);
                return;
              }
              for (Infraction violation : violations)
                sender.sendMessage(
                    MessageUtil.getMessage(
                        Message.LOGS_ENTRY,
                        "server",
                        violation.serverName(),
                        "player",
                        violation.playerName(),
                        "check",
                        violation.moduleName(),
                        "vl",
                        String.valueOf(violation.vl()),
                        "verbose",
                        violation.verbose(),
                        "timeago",
                        TimeUtil.formatTimeAgo(violation.createdAt(), localeManager)));
            });
  }

  private long parseTime(String timeArg) {
    if (timeArg == null) return 0L;
    try {
      if (timeArg.length() < 2) return -1L;
      long value = Long.parseLong(timeArg.substring(0, timeArg.length() - 1));
      char unit = Character.toLowerCase(timeArg.charAt(timeArg.length() - 1));
      long multiplier =
          switch (unit) {
            case 'm' -> TimeUnit.MINUTES.toMillis(1);
            case 'h' -> TimeUnit.HOURS.toMillis(1);
            case 'd' -> TimeUnit.DAYS.toMillis(1);
            default -> -1L;
          };
      if (multiplier == -1L) return -1L;
      return System.currentTimeMillis() - value * multiplier;
    } catch (NumberFormatException e) {
      return -1L;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/logs/parser/TimeFlagDecoder.java ---

package club.nezxenka.netvision.service.command.impl.logs.parser;

import java.util.concurrent.TimeUnit;

public class TimeFlagDecoder {

  public long decode(String timeArg) {
    if (timeArg == null) return 0L;
    try {
      if (timeArg.length() < 2) return -1L;
      long value = Long.parseLong(timeArg.substring(0, timeArg.length() - 1));
      char unit = Character.toLowerCase(timeArg.charAt(timeArg.length() - 1));
      long multiplier =
          switch (unit) {
            case 'm' -> TimeUnit.MINUTES.toMillis(1);
            case 'h' -> TimeUnit.HOURS.toMillis(1);
            case 'd' -> TimeUnit.DAYS.toMillis(1);
            default -> -1L;
          };
      if (multiplier == -1L) return -1L;
      return System.currentTimeMillis() - value * multiplier;
    } catch (NumberFormatException e) {
      return -1L;
    }
  }

  public boolean isValid(String timeArg) {
    return decode(timeArg) != -1L;
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/menu/MenuCommand.java ---

package club.nezxenka.netvision.service.command.impl.menu;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import club.nezxenka.netvision.visual.menu.coop.ChickenCoopMenu;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class MenuCommand implements NetVisionCommand {
  private final ChickenCoopMenu chickenCoopMenu;

  public MenuCommand(ChickenCoopMenu chickenCoopMenu) {
    this.chickenCoopMenu = chickenCoopMenu;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("menu")
            .permission("netvision.menu")
            .handler(this::execute));
  }

  private void execute(CommandContext<Sender> context) {
    CommandSender sender = context.sender().getNativeSender();
    if (!(sender instanceof Player player)) {
      MessageUtil.sendMessage(sender, Message.RUN_AS_PLAYER);
      return;
    }
    chickenCoopMenu.openMenu(player);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/menu/opener/CoopMenuOpener.java ---

package club.nezxenka.netvision.service.command.impl.menu.opener;

import club.nezxenka.netvision.visual.menu.coop.ChickenCoopMenu;
import org.bukkit.entity.Player;

public class CoopMenuOpener {
  private final ChickenCoopMenu menu;

  public CoopMenuOpener(ChickenCoopMenu menu) {
    this.menu = menu;
  }

  public void open(Player player) {
    menu.openMenu(player);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/prob/ProbCommand.java ---

package club.nezxenka.netvision.service.command.impl.prob;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.service.command.framework.CommandRegistrationService;
import club.nezxenka.netvision.service.command.requirement.PlayerSenderRequirement;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.Locale;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.TextComponent;
import net.kyori.adventure.text.format.NamedTextColor;
import net.kyori.adventure.text.format.TextColor;
import net.kyori.adventure.text.format.TextDecoration;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.player.PlayerQuitEvent;
import org.bukkit.scheduler.BukkitTask;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.bukkit.parser.PlayerParser;
import org.incendo.cloud.context.CommandContext;

public class ProbCommand implements NetVisionCommand, Listener {
  private final Map<UUID, ProbSession> activeSessions = new ConcurrentHashMap<>();
  private final PlayerDataManager playerDataManager;
  private final LocaleManager localeManager;
  private final NetVision plugin;

  public ProbCommand(
      PlayerDataManager playerDataManager, LocaleManager localeManager, NetVision plugin) {
    this.playerDataManager = playerDataManager;
    this.localeManager = localeManager;
    this.plugin = plugin;
    plugin.getServer().getPluginManager().registerEvents(this, plugin);
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("prob")
            .permission("netvision.prob")
            .required("target", PlayerParser.playerParser())
            .apply(
                CommandRegistrationService.REQUIREMENT_FACTORY.create(
                    PlayerSenderRequirement.PLAYER_SENDER_REQUIREMENT))
            .handler(this::execute));
  }

  @EventHandler
  public void onPlayerQuit(PlayerQuitEvent event) {
    final Player player = event.getPlayer();
    final UUID uuid = player.getUniqueId();
    if (activeSessions.containsKey(uuid)) stop(player);
    UUID viewerUuid = null;
    for (Map.Entry<UUID, ProbSession> entry : activeSessions.entrySet()) {
      if (entry.getValue().targetUuid().equals(uuid)) {
        viewerUuid = entry.getKey();
        break;
      }
    }
    if (viewerUuid != null) {
      Player viewer = Bukkit.getPlayer(viewerUuid);
      if (viewer != null) {
        stop(viewer);
        MessageUtil.sendMessage(viewer, Message.PROB_DISABLED, "player", player.getName());
      } else activeSessions.remove(viewerUuid);
    }
  }

  private void execute(CommandContext<Sender> context) {
    final Player player = context.sender().getPlayer();
    final Player target = context.get("target");
    final ProbSession session = activeSessions.get(player.getUniqueId());
    if (session != null && session.targetUuid().equals(target.getUniqueId())) {
      stop(player);
      MessageUtil.sendMessage(player, Message.PROB_DISABLED, "player", target.getName());
      return;
    }
    if (session != null) stop(player);
    start(player, target);
    MessageUtil.sendMessage(player, Message.PROB_ENABLED, "player", target.getName());
  }

  private void start(Player viewer, Player target) {
    final UUID viewerId = viewer.getUniqueId();
    final UUID targetId = target.getUniqueId();
    final ActionBarComponents components = new ActionBarComponents(localeManager);
    final BukkitTask task =
        plugin
            .getServer()
            .getScheduler()
            .runTaskTimer(
                plugin,
                () -> {
                  final Player onlineViewer = Bukkit.getPlayer(viewerId);
                  final Player onlineTarget = Bukkit.getPlayer(targetId);
                  if (onlineViewer == null
                      || !onlineViewer.isOnline()
                      || onlineTarget == null
                      || !onlineTarget.isOnline()) {
                    if (onlineViewer != null) stop(onlineViewer);
                    return;
                  }
                  final NetVisionPlayer nvTarget = playerDataManager.getPlayer(onlineTarget);
                  if (nvTarget == null) {
                    sendActionBar(
                        onlineViewer,
                        MessageUtil.getMessage(
                            Message.PROB_NO_DATA, "player", onlineTarget.getName()));
                    return;
                  }
                  final NeuralAnalyzer aiCheck =
                      nvTarget.getModuleCoordinator().getModule(NeuralAnalyzer.class);
                  if (aiCheck == null) {
                    sendActionBar(
                        onlineViewer,
                        MessageUtil.getMessage(
                            Message.PROB_NO_AICHECK, "player", onlineTarget.getName()));
                    return;
                  }
                  sendActionBar(onlineViewer, buildActionBar(aiCheck, onlineTarget, components));
                },
                0L,
                2L);
    final ProbSession newSession = new ProbSession(targetId, task, components);
    activeSessions.put(viewerId, newSession);
  }

  private void stop(Player viewer) {
    final ProbSession session = activeSessions.remove(viewer.getUniqueId());
    if (session != null) {
      session.task().cancel();
      sendActionBar(viewer, Component.empty());
    }
  }

  private Component buildActionBar(
      NeuralAnalyzer aiCheck, Player target, ActionBarComponents components) {
    final double probability = aiCheck.getLastProbability();
    final double violationLevel = aiCheck.getBuffer();
    final int ping = target.getPing();
    final TextColor probColor = getProbColor(probability);
    final TextColor vlColor = getVlColor(violationLevel);
    final TextColor pingColor = getPingColor(ping);
    TextComponent bufferComponent =
        Component.text(String.format(Locale.US, "%.2f", violationLevel), vlColor);
    if (violationLevel > 30) bufferComponent = bufferComponent.decorate(TextDecoration.BOLD);
    return Component.text()
        .append(components.labelProb().color(probColor))
        .append(components.openParen().color(probColor))
        .append(Component.text(target.getName(), probColor))
        .append(components.closeParen().color(probColor))
        .append(Component.text(String.format(Locale.US, "%.4f", probability), probColor))
        .append(components.separator())
        .append(components.labelBuffer().color(vlColor))
        .append(components.colon().color(vlColor))
        .append(bufferComponent)
        .append(components.separator())
        .append(components.labelPing().color(pingColor))
        .append(components.colon().color(pingColor))
        .append(Component.text(ping, pingColor))
        .append(components.suffixPing().color(pingColor))
        .build();
  }

  private void sendActionBar(Player player, Component message) {
    if (player == null || !player.isOnline()) return;
    plugin.getAdventure().player(player).sendActionBar(message);
  }

  private TextColor getProbColor(double probability) {
    if (probability > 0.9) return NamedTextColor.RED;
    if (probability > 0.5) return NamedTextColor.YELLOW;
    return NamedTextColor.GREEN;
  }

  private TextColor getVlColor(double violationLevel) {
    if (violationLevel > 30) return NamedTextColor.DARK_RED;
    if (violationLevel > 15) return NamedTextColor.RED;
    return NamedTextColor.GREEN;
  }

  private TextColor getPingColor(int ping) {
    if (ping > 150) return NamedTextColor.RED;
    if (ping > 80) return NamedTextColor.YELLOW;
    return NamedTextColor.GREEN;
  }

  private record ProbSession(UUID targetUuid, BukkitTask task, ActionBarComponents components) {}

  private record ActionBarComponents(
      Component labelProb,
      Component labelBuffer,
      Component labelPing,
      Component separator,
      Component suffixPing,
      Component openParen,
      Component closeParen,
      Component colon) {
    ActionBarComponents(LocaleManager lm) {
      this(
          Component.text(lm.getRawMessage(Message.PROB_FORMAT_LABEL_PROB)),
          Component.text(lm.getRawMessage(Message.PROB_FORMAT_LABEL_BUFFER)),
          Component.text(lm.getRawMessage(Message.PROB_FORMAT_LABEL_PING)),
          Component.text(lm.getRawMessage(Message.PROB_FORMAT_SEPARATOR), NamedTextColor.DARK_GRAY),
          Component.text(lm.getRawMessage(Message.PROB_FORMAT_SUFFIX_PING)),
          Component.text(" ("),
          Component.text("): "),
          Component.text(": "));
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/prob/renderer/ProbActionBarComposer.java ---

package club.nezxenka.netvision.service.command.impl.prob.renderer;

import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.util.message.Message;
import java.util.Locale;
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.TextComponent;
import net.kyori.adventure.text.format.NamedTextColor;
import net.kyori.adventure.text.format.TextColor;
import net.kyori.adventure.text.format.TextDecoration;
import org.bukkit.entity.Player;

public class ProbActionBarComposer {
  private final LocaleManager localeManager;

  public ProbActionBarComposer(LocaleManager localeManager) {
    this.localeManager = localeManager;
  }

  public Component compose(NeuralAnalyzer check, Player target) {
    double probability = check.getLastProbability();
    double buffer = check.getBuffer();
    int ping = target.getPing();
    TextColor probColor =
        probability > 0.9
            ? NamedTextColor.RED
            : (probability > 0.5 ? NamedTextColor.YELLOW : NamedTextColor.GREEN);
    TextColor vlColor =
        buffer > 30
            ? NamedTextColor.DARK_RED
            : (buffer > 15 ? NamedTextColor.RED : NamedTextColor.GREEN);
    TextColor pingColor =
        ping > 150
            ? NamedTextColor.RED
            : (ping > 80 ? NamedTextColor.YELLOW : NamedTextColor.GREEN);
    TextComponent bufferComp = Component.text(String.format(Locale.US, "%.2f", buffer), vlColor);
    if (buffer > 30) bufferComp = bufferComp.decorate(TextDecoration.BOLD);
    return Component.text()
        .append(
            Component.text(localeManager.getRawMessage(Message.PROB_FORMAT_LABEL_PROB))
                .color(probColor))
        .append(Component.text(" (").color(probColor))
        .append(Component.text(target.getName(), probColor))
        .append(Component.text("): ").color(probColor))
        .append(Component.text(String.format(Locale.US, "%.4f", probability), probColor))
        .append(
            Component.text(
                localeManager.getRawMessage(Message.PROB_FORMAT_SEPARATOR),
                NamedTextColor.DARK_GRAY))
        .append(
            Component.text(localeManager.getRawMessage(Message.PROB_FORMAT_LABEL_BUFFER))
                .color(vlColor))
        .append(Component.text(": ").color(vlColor))
        .append(bufferComp)
        .append(
            Component.text(
                localeManager.getRawMessage(Message.PROB_FORMAT_SEPARATOR),
                NamedTextColor.DARK_GRAY))
        .append(
            Component.text(localeManager.getRawMessage(Message.PROB_FORMAT_LABEL_PING))
                .color(pingColor))
        .append(Component.text(": ").color(pingColor))
        .append(Component.text(ping, pingColor))
        .append(
            Component.text(localeManager.getRawMessage(Message.PROB_FORMAT_SUFFIX_PING))
                .color(pingColor))
        .build();
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/prob/session/ProbSessionRegistry.java ---

package club.nezxenka.netvision.service.command.impl.prob.session;

import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class ProbSessionRegistry {
  private final Map<UUID, ProbSessionEntry> sessions = new ConcurrentHashMap<>();

  public record ProbSessionEntry(UUID targetUuid, org.bukkit.scheduler.BukkitTask task) {}

  public void register(UUID viewer, ProbSessionEntry entry) {
    sessions.put(viewer, entry);
  }

  public ProbSessionEntry get(UUID viewer) {
    return sessions.get(viewer);
  }

  public ProbSessionEntry remove(UUID viewer) {
    return sessions.remove(viewer);
  }

  public boolean contains(UUID viewer) {
    return sessions.containsKey(viewer);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/profile/gatherer/PlayerStatGatherer.java ---

package club.nezxenka.netvision.service.command.impl.profile.gatherer;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.aim.AimEvaluator;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import org.bukkit.Statistic;
import org.bukkit.entity.Player;

public class PlayerStatGatherer {

  public record ProfileStats(
      String playerName,
      int ping,
      String version,
      String brand,
      long sessionMillis,
      long totalPlayMillis,
      String sensX,
      String sensY,
      String aiBuffer,
      String prob90) {}

  public ProfileStats gather(NetVisionPlayer nvPlayer, Player target) {
    AimEvaluator aim = nvPlayer.getModuleCoordinator().getModule(AimEvaluator.class);
    NeuralAnalyzer ai = nvPlayer.getModuleCoordinator().getModule(NeuralAnalyzer.class);
    long session = System.currentTimeMillis() - nvPlayer.getJoinTime();
    long totalTicks = 0;
    try {
      totalTicks = target.getStatistic(Statistic.PLAY_ONE_MINUTE);
    } catch (IllegalArgumentException e) {
      totalTicks = 0;
    }
    return new ProfileStats(
        target.getName(),
        target.getPing(),
        nvPlayer.getUser().getClientVersion().getReleaseName(),
        nvPlayer.getBrand(),
        session,
        totalTicks * 50L,
        aim != null ? String.format("%.2f", aim.getSensitivityX() * 200) : "N/A",
        aim != null ? String.format("%.2f", aim.getSensitivityY() * 200) : "N/A",
        ai != null ? String.format("%.2f", ai.getBuffer()) : "N/A",
        ai != null ? String.valueOf(ai.getProb90()) : "N/A");
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/profile/ProfileCommand.java ---

package club.nezxenka.netvision.service.command.impl.profile;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.engine.aim.AimEvaluator;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import club.nezxenka.netvision.util.time.TimeUtil;
import org.bukkit.Statistic;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.bukkit.parser.PlayerParser;
import org.incendo.cloud.context.CommandContext;

public class ProfileCommand implements NetVisionCommand {

  private final PlayerDataManager playerDataManager;
  private final LocaleManager localeManager;

  public ProfileCommand(PlayerDataManager playerDataManager, LocaleManager localeManager) {
    this.playerDataManager = playerDataManager;
    this.localeManager = localeManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("profile")
            .permission("netvision.profile")
            .required("target", PlayerParser.playerParser())
            .handler(this::execute));
  }

  private void execute(CommandContext<Sender> context) {
    final CommandSender sender = context.sender().getNativeSender();
    final Player target = context.get("target");
    NetVisionPlayer nvPlayer = playerDataManager.getPlayer(target);
    if (nvPlayer == null) {
      MessageUtil.sendMessage(sender, Message.PROFILE_NO_DATA);
      return;
    }
    NeuralAnalyzer aiCheck = nvPlayer.getModuleCoordinator().getModule(NeuralAnalyzer.class);
    AimEvaluator aimProcessor = nvPlayer.getModuleCoordinator().getModule(AimEvaluator.class);
    long sessionMillis = System.currentTimeMillis() - nvPlayer.getJoinTime();
    long totalPlayTicks = 0;
    try {
      totalPlayTicks = target.getStatistic(Statistic.PLAY_ONE_MINUTE);
    } catch (IllegalArgumentException e) {
      totalPlayTicks = 0;
    }
    long totalPlayMillis = totalPlayTicks * 50;
    MessageUtil.sendMessageList(
        sender,
        Message.PROFILE_LINES,
        "player",
        target.getName(),
        "ping",
        String.valueOf(target.getPing()),
        "version",
        nvPlayer.getUser().getClientVersion().getReleaseName(),
        "brand",
        nvPlayer.getBrand(),
        "session_time",
        TimeUtil.formatDuration(sessionMillis, localeManager),
        "total_playtime",
        TimeUtil.formatDuration(totalPlayMillis, localeManager),
        "sens_x",
        aimProcessor != null ? String.format("%.2f", aimProcessor.getSensitivityX() * 200) : "N/A",
        "sens_y",
        aimProcessor != null ? String.format("%.2f", aimProcessor.getSensitivityY() * 200) : "N/A",
        "ai_buffer",
        aiCheck != null ? String.format("%.2f", aiCheck.getBuffer()) : "N/A",
        "ai_probs_90",
        aiCheck != null ? String.valueOf(aiCheck.getProb90()) : "N/A");
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/punish/PunishCommand.java ---

package club.nezxenka.netvision.service.command.impl.punish;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.bukkit.OfflinePlayer;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.bukkit.parser.OfflinePlayerParser;
import org.incendo.cloud.context.CommandContext;

public class PunishCommand implements NetVisionCommand {
  private final DatabaseManager databaseManager;

  public PunishCommand(DatabaseManager databaseManager) {
    this.databaseManager = databaseManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    final var baseBuilder =
        manager.commandBuilder(rootName).literal("punish").permission("netvision.punish.manage");
    manager.command(
        baseBuilder
            .literal("reset")
            .required("target", OfflinePlayerParser.offlinePlayerParser())
            .handler(this::reset));
  }

  private void reset(CommandContext<Sender> context) {
    final Sender sender = context.sender();
    final OfflinePlayer target = context.get("target");
    databaseManager.getDatabase().resetAllViolationLevels(target.getUniqueId());
    MessageUtil.sendMessage(
        sender.getNativeSender(), Message.PUNISH_RESET_SUCCESS, "player", target.getName());
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/punish/resetter/ViolationResetHandler.java ---

package club.nezxenka.netvision.service.command.impl.punish.resetter;

import club.nezxenka.netvision.core.storage.api.RecordStorage;
import java.util.UUID;

public class ViolationResetHandler {
  private final RecordStorage database;

  public ViolationResetHandler(RecordStorage database) {
    this.database = database;
  }

  public void resetAll(UUID playerUuid) {
    database.resetAllViolationLevels(playerUuid);
  }

  public void resetGroup(UUID playerUuid, String group) {
    database.resetViolationLevel(playerUuid, group);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/reload/executor/PluginReloadTask.java ---

package club.nezxenka.netvision.service.command.impl.reload.executor;

import club.nezxenka.netvision.NetVision;

public class PluginReloadTask {
  private final NetVision plugin;

  public PluginReloadTask(NetVision plugin) {
    this.plugin = plugin;
  }

  public void execute() {
    plugin.reloadPlugin();
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/reload/ReloadCommand.java ---

package club.nezxenka.netvision.service.command.impl.reload;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class ReloadCommand implements NetVisionCommand {
  private final NetVision plugin;

  public ReloadCommand(NetVision plugin) {
    this.plugin = plugin;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("reload")
            .permission("netvision.reload")
            .handler(this::execute));
  }

  private void execute(CommandContext<Sender> context) {
    MessageUtil.sendMessage(context.sender().getNativeSender(), Message.RELOAD_START);
    plugin.reloadPlugin();
    MessageUtil.sendMessage(context.sender().getNativeSender(), Message.RELOAD_SUCCESS);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/stats/counter/StatsAggregationService.java ---

package club.nezxenka.netvision.service.command.impl.stats.counter;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import java.util.concurrent.TimeUnit;

public class StatsAggregationService {
  private final RecordStorage database;
  private final PlayerDataManager playerDataManager;

  public StatsAggregationService(RecordStorage database, PlayerDataManager playerDataManager) {
    this.database = database;
    this.playerDataManager = playerDataManager;
  }

  public record AggregatedStats(
      int dailyFlags, int dailyViolators, int onlinePlayers, long suspiciousNow) {}

  public AggregatedStats aggregate() {
    long since = System.currentTimeMillis() - TimeUnit.DAYS.toMillis(1);
    int flags = database.getLogCount(since);
    int violators = database.getUniqueViolatorsSince(since);
    int online = org.bukkit.Bukkit.getOnlinePlayers().size();
    long suspicious =
        playerDataManager.getPlayers().stream()
            .filter(
                p -> {
                  NeuralAnalyzer c = p.getModuleCoordinator().getModule(NeuralAnalyzer.class);
                  return c != null && c.getBuffer() > 10;
                })
            .count();
    return new AggregatedStats(flags, violators, online, suspicious);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/stats/StatsCommand.java ---

package club.nezxenka.netvision.service.command.impl.stats;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.core.storage.connection.DatabaseManager;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.concurrent.TimeUnit;
import org.bukkit.Bukkit;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;

public class StatsCommand implements NetVisionCommand {
  private final NetVision plugin;
  private final DatabaseManager databaseManager;
  private final PlayerDataManager playerDataManager;

  public StatsCommand(
      NetVision plugin, DatabaseManager databaseManager, PlayerDataManager playerDataManager) {
    this.plugin = plugin;
    this.databaseManager = databaseManager;
    this.playerDataManager = playerDataManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("stats")
            .permission("netvision.stats")
            .handler(this::execute));
  }

  private void execute(CommandContext<Sender> context) {
    final Sender sender = context.sender();
    final RecordStorage db = databaseManager.getDatabase();
    if (db == null) {
      sender.sendMessage(MessageUtil.getMessage(Message.HISTORY_DISABLED));
      return;
    }
    Bukkit.getScheduler()
        .runTaskAsynchronously(
            plugin,
            () -> {
              long since = System.currentTimeMillis() - TimeUnit.DAYS.toMillis(1);
              int totalFlags = db.getLogCount(since);
              int uniqueViolators = db.getUniqueViolatorsSince(since);
              Bukkit.getScheduler()
                  .runTask(
                      plugin,
                      () ->
                          MessageUtil.sendMessageList(
                              sender.getNativeSender(),
                              Message.STATS_LINES,
                              "flags_24h",
                              String.valueOf(totalFlags),
                              "violators_24h",
                              String.valueOf(uniqueViolators),
                              "online_players",
                              String.valueOf(Bukkit.getOnlinePlayers().size()),
                              "suspicious_now",
                              String.valueOf(getSuspiciousCount())));
            });
  }

  private long getSuspiciousCount() {
    return playerDataManager.getPlayers().stream()
        .filter(
            sp -> {
              NeuralAnalyzer check = sp.getModuleCoordinator().getModule(NeuralAnalyzer.class);
              return check != null && check.getBuffer() > 10;
            })
        .count();
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/status/StatusCommand.java ---

package club.nezxenka.netvision.service.command.impl.status;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.service.hologram.internal.HologramManager;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import org.bukkit.Bukkit;
import org.bukkit.ChatColor;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.bukkit.parser.PlayerParser;
import org.incendo.cloud.context.CommandContext;

public class StatusCommand implements NetVisionCommand {
  private final HologramManager hologramManager;

  public StatusCommand(HologramManager hologramManager) {
    this.hologramManager = hologramManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("status")
            .permission("netvision.status")
            .handler(this::executeAll));
    manager.command(
        manager
            .commandBuilder(rootName)
            .literal("status")
            .permission("netvision.status")
            .required("target", PlayerParser.playerParser())
            .handler(this::executeTarget));
  }

  private void executeAll(CommandContext<Sender> context) {
    CommandSender sender = context.sender().getNativeSender();
    if (!(sender instanceof Player player)) {
      MessageUtil.sendMessage(sender, Message.RUN_AS_PLAYER);
      return;
    }
    boolean hasAny = false;
    for (Player target : Bukkit.getOnlinePlayers()) {
      if (hologramManager.isEnabled(player, target)) {
        hasAny = true;
        break;
      }
    }
    if (hasAny) {
      hologramManager.disableForAll(player);
      player.sendMessage(ChatColor.YELLOW + "Голограммы отключены для всех игроков.");
    } else {
      hologramManager.enableForAll(player);
      player.sendMessage(ChatColor.GREEN + "Голограммы включены для всех игроков.");
    }
  }

  private void executeTarget(CommandContext<Sender> context) {
    CommandSender sender = context.sender().getNativeSender();
    if (!(sender instanceof Player player)) {
      MessageUtil.sendMessage(sender, Message.RUN_AS_PLAYER);
      return;
    }
    Player target = context.get("target");
    if (target.hasPermission("netvision.exempt")) {
      player.sendMessage(ChatColor.RED + "Этот игрок освобожден от проверок.");
      return;
    }
    if (hologramManager.isEnabled(player, target)) {
      hologramManager.disableHologram(player, target);
      player.sendMessage(
          ChatColor.YELLOW
              + "Голограмма отключена для игрока "
              + ChatColor.WHITE
              + target.getName());
    } else {
      hologramManager.enableHologram(player, target);
      player.sendMessage(
          ChatColor.GREEN + "Голограмма включена для игрока " + ChatColor.WHITE + target.getName());
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/status/toggler/HologramToggleService.java ---

package club.nezxenka.netvision.service.command.impl.status.toggler;

import club.nezxenka.netvision.service.hologram.internal.HologramManager;
import org.bukkit.entity.Player;

public class HologramToggleService {
  private final HologramManager manager;

  public HologramToggleService(HologramManager manager) {
    this.manager = manager;
  }

  public boolean toggleAll(Player viewer) {
    boolean hasAny = false;
    for (Player target : org.bukkit.Bukkit.getOnlinePlayers()) {
      if (manager.isEnabled(viewer, target)) {
        hasAny = true;
        break;
      }
    }
    if (hasAny) manager.disableForAll(viewer);
    else manager.enableForAll(viewer);
    return !hasAny;
  }

  public boolean toggleOne(Player viewer, Player target) {
    if (target.hasPermission("netvision.exempt")) return false;
    if (manager.isEnabled(viewer, target)) {
      manager.disableHologram(viewer, target);
      return false;
    } else {
      manager.enableHologram(viewer, target);
      return true;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/suspicious/filter/SuspiciousPlayerFilter.java ---

package club.nezxenka.netvision.service.command.impl.suspicious.filter;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import java.util.List;
import java.util.stream.Collectors;

public class SuspiciousPlayerFilter {
  public List<NetVisionPlayer> filter(List<NetVisionPlayer> players, double minBuffer) {
    return players.stream()
        .filter(
            p -> {
              NeuralAnalyzer c = p.getModuleCoordinator().getModule(NeuralAnalyzer.class);
              return c != null && c.getBuffer() > minBuffer;
            })
        .collect(Collectors.toList());
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/suspicious/sorter/SuspiciousBufferComparator.java ---

package club.nezxenka.netvision.service.command.impl.suspicious.sorter;

import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import java.util.Comparator;

public class SuspiciousBufferComparator implements Comparator<NetVisionPlayer> {
  @Override
  public int compare(NetVisionPlayer a, NetVisionPlayer b) {
    NeuralAnalyzer ca = a.getModuleCoordinator().getModule(NeuralAnalyzer.class);
    NeuralAnalyzer cb = b.getModuleCoordinator().getModule(NeuralAnalyzer.class);
    double ba = ca != null ? ca.getBuffer() : 0;
    double bb = cb != null ? cb.getBuffer() : 0;
    return Double.compare(bb, ba);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/impl/suspicious/SuspiciousCommand.java ---

package club.nezxenka.netvision.service.command.impl.suspicious;

import club.nezxenka.netvision.actor.manager.PlayerDataManager;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.engine.network.neural.NeuralAnalyzer;
import club.nezxenka.netvision.service.command.api.NetVisionCommand;
import club.nezxenka.netvision.service.command.framework.CommandRegistrationService;
import club.nezxenka.netvision.service.command.requirement.PlayerSenderRequirement;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;
import net.kyori.adventure.text.Component;
import org.bukkit.entity.Player;
import org.incendo.cloud.CommandManager;
import org.incendo.cloud.context.CommandContext;
import org.incendo.cloud.parser.standard.DoubleParser;

public class SuspiciousCommand implements NetVisionCommand {
  private final PlayerDataManager playerDataManager;
  private final SignalManager alertManager;

  public SuspiciousCommand(PlayerDataManager playerDataManager, SignalManager alertManager) {
    this.playerDataManager = playerDataManager;
    this.alertManager = alertManager;
  }

  @Override
  public void register(CommandManager<Sender> manager, String rootName) {
    final var base =
        manager.commandBuilder(rootName).literal("suspicious").permission("netvision.suspicious");
    manager.command(
        base.literal("alerts")
            .permission("netvision.suspicious.alerts")
            .apply(
                CommandRegistrationService.REQUIREMENT_FACTORY.create(
                    PlayerSenderRequirement.PLAYER_SENDER_REQUIREMENT))
            .handler(this::executeAlerts));
    manager.command(
        base.literal("list")
            .permission("netvision.suspicious.list")
            .flag(manager.flagBuilder("buffer").withComponent(DoubleParser.doubleParser(0.0)))
            .handler(this::executeList));
    manager.command(
        base.literal("top").permission("netvision.suspicious.top").handler(this::executeTop));
  }

  private void executeAlerts(CommandContext<Sender> context) {
    final Player player = context.sender().getPlayer();
    alertManager.toggle(player, SignalType.SUSPICIOUS, false);
  }

  private void executeList(CommandContext<Sender> context) {
    final Sender sender = context.sender();
    final Double bufferFlag = context.flags().get("buffer");
    final double bufferFilter = bufferFlag != null ? bufferFlag : 0.0;
    List<NetVisionPlayer> suspiciousPlayers =
        playerDataManager.getPlayers().stream()
            .filter(
                sp -> {
                  NeuralAnalyzer check = sp.getModuleCoordinator().getModule(NeuralAnalyzer.class);
                  return check != null && check.getBuffer() > bufferFilter;
                })
            .sorted(
                Comparator.comparingDouble(
                    sp -> -sp.getModuleCoordinator().getModule(NeuralAnalyzer.class).getBuffer()))
            .collect(Collectors.toList());
    if (suspiciousPlayers.isEmpty()) {
      sender.sendMessage(MessageUtil.getMessage(Message.SUSPICIOUS_LIST_EMPTY));
      return;
    }
    sender.sendMessage(
        MessageUtil.getMessage(
            Message.SUSPICIOUS_LIST_HEADER, "count", String.valueOf(suspiciousPlayers.size())));
    for (NetVisionPlayer sp : suspiciousPlayers) {
      NeuralAnalyzer aiCheck = sp.getModuleCoordinator().getModule(NeuralAnalyzer.class);
      double buffer = aiCheck.getBuffer();
      String playerName = sp.getPlayer().getName();
      Component entry =
          MessageUtil.getMessage(
              Message.SUSPICIOUS_LIST_ENTRY,
              "player",
              playerName,
              "buffer",
              String.format("%.1f", buffer),
              "ping",
              String.valueOf(sp.getPlayer().getPing()));
      sender.sendMessage(entry);
    }
  }

  private void executeTop(CommandContext<Sender> context) {
    final Sender sender = context.sender();
    NetVisionPlayer topPlayer =
        playerDataManager.getPlayers().stream()
            .filter(sp -> sp.getModuleCoordinator().getModule(NeuralAnalyzer.class) != null)
            .max(
                Comparator.comparingDouble(
                    sp -> sp.getModuleCoordinator().getModule(NeuralAnalyzer.class).getBuffer()))
            .orElse(null);
    if (topPlayer == null
        || topPlayer.getModuleCoordinator().getModule(NeuralAnalyzer.class).getBuffer() == 0) {
      sender.sendMessage(MessageUtil.getMessage(Message.SUSPICIOUS_TOP_NONE));
      return;
    }
    String playerName = topPlayer.getPlayer().getName();
    double buffer = topPlayer.getModuleCoordinator().getModule(NeuralAnalyzer.class).getBuffer();
    Component message =
        MessageUtil.getMessage(
            Message.SUSPICIOUS_TOP_PLAYER,
            "player",
            playerName,
            "buffer",
            String.format("%.1f", buffer));
    sender.sendMessage(message);
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/requirement/checker/RequirementEvaluationService.java ---

package club.nezxenka.netvision.service.command.requirement.checker;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.SenderRequirement;
import org.incendo.cloud.context.CommandContext;

public class RequirementEvaluationService {
  public boolean evaluate(SenderRequirement requirement, CommandContext<Sender> context) {
    return requirement.evaluateRequirement(context);
  }

  public boolean isPlayer(CommandContext<Sender> context) {
    return context.sender().isPlayer();
  }
}


--- src/main/java/club/nezxenka/netvision/service/command/requirement/PlayerSenderRequirement.java ---

package club.nezxenka.netvision.service.command.requirement;

import club.nezxenka.netvision.audience.api.Sender;
import club.nezxenka.netvision.service.command.api.SenderRequirement;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import net.kyori.adventure.text.Component;
import org.checkerframework.checker.nullness.qual.NonNull;
import org.incendo.cloud.context.CommandContext;

public final class PlayerSenderRequirement implements SenderRequirement {
  public static final PlayerSenderRequirement PLAYER_SENDER_REQUIREMENT =
      new PlayerSenderRequirement();

  @Override
  public @NonNull Component errorMessage(Sender sender) {
    return MessageUtil.getMessage(Message.RUN_AS_PLAYER);
  }

  @Override
  public boolean evaluateRequirement(@NonNull CommandContext<Sender> commandContext) {
    return commandContext.sender().isPlayer();
  }
}


--- src/main/java/club/nezxenka/netvision/service/enforce/internal/action/EnforcementActionType.java ---

package club.nezxenka.netvision.service.enforce.internal.action;

public enum EnforcementActionType {
  ALERT,
  LOG,
  RESET,
  BROADCAST,
  CONSOLE_COMMAND,
  KICK,
  BAN
}


--- src/main/java/club/nezxenka/netvision/service/enforce/internal/EnforcementManager.java ---

package club.nezxenka.netvision.service.enforce.internal;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.engine.api.AnalysisModule;
import club.nezxenka.netvision.service.enforce.model.EnforcementGroup;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.HashMap;
import java.util.List;
import java.util.Locale;
import java.util.Map;
import java.util.NavigableMap;
import java.util.TreeMap;
import net.kyori.adventure.text.Component;
import org.bukkit.Bukkit;
import org.bukkit.configuration.ConfigurationSection;

public class EnforcementManager {
  private final NetVisionPlayer nvPlayer;
  private final NetVision plugin;
  private final ConfigManager configManager;
  private final Map<String, EnforcementGroup> punishmentGroups = new HashMap<>();
  private final SignalManager alertManager;
  private final RecordStorage database;

  public EnforcementManager(
      NetVisionPlayer nvPlayer,
      NetVision plugin,
      ConfigManager configManager,
      RecordStorage database,
      SignalManager alertManager) {
    this.nvPlayer = nvPlayer;
    this.plugin = plugin;
    this.configManager = configManager;
    this.alertManager = alertManager;
    this.database = database;
    reload();
  }

  public void reload() {
    punishmentGroups.clear();
    ConfigurationSection punishmentsSection =
        configManager.getPunishments().getConfigurationSection("Punishments");
    if (punishmentsSection == null) return;
    for (String groupName : punishmentsSection.getKeys(false)) {
      ConfigurationSection groupSection = punishmentsSection.getConfigurationSection(groupName);
      if (groupSection == null) continue;
      List<String> moduleNamesFilters = groupSection.getStringList("checks");
      ConfigurationSection actionsSection = groupSection.getConfigurationSection("actions");
      if (actionsSection == null) continue;
      NavigableMap<Integer, List<String>> parsedActions = new TreeMap<>();
      for (String vlString : actionsSection.getKeys(false)) {
        try {
          int vl = Integer.parseInt(vlString);
          List<String> commands = actionsSection.getStringList(vlString);
          parsedActions.put(vl, commands);
        } catch (NumberFormatException e) {
          plugin
              .getLogger()
              .warning("Invalid VL " + vlString + " in punishment group " + groupName + ".");
        }
      }
      if (!parsedActions.isEmpty()) {
        EnforcementGroup punishGroup =
            new EnforcementGroup(groupName, moduleNamesFilters, parsedActions);
        punishmentGroups.put(groupName, punishGroup);
      }
    }
  }

  public void handleFlag(AnalysisModule module, String debug) {
    for (EnforcementGroup group : punishmentGroups.values()) {
      if (group.isModuleAssociated(module)) {
        Bukkit.getScheduler()
            .runTaskAsynchronously(
                plugin,
                () -> {
                  int newVl =
                      database.incrementViolationLevel(nvPlayer.getUuid(), group.getGroupName());
                  Map.Entry<Integer, List<String>> entry = group.getActions().floorEntry(newVl);
                  if (entry != null) executeCommands(module, group, newVl, debug, entry.getValue());
                });
      }
    }
  }

  private void executeCommands(
      AnalysisModule module,
      EnforcementGroup group,
      int vl,
      String verbose,
      List<String> commands) {
    for (String command : commands) {
      String commandLower = command.toLowerCase(Locale.ROOT);
      if (commandLower.equals("[alert]")) sendAlert(module, vl, verbose);
      else if (commandLower.equals("[log]"))
        database.logAlert(nvPlayer, verbose, module.getModuleName(), vl);
      else if (commandLower.equals("[reset]"))
        database.resetViolationLevel(nvPlayer.getUuid(), group.getGroupName());
      else if (commandLower.startsWith("[broadcast] ")) {
        final String message = command.substring("[broadcast] ".length());
        final Component component =
            MessageUtil.format(
                message,
                "player",
                nvPlayer.getPlayer().getName(),
                "check_name",
                module.getModuleName(),
                "vl",
                String.valueOf(vl),
                "verbose",
                verbose);
        Bukkit.getScheduler()
            .runTask(plugin, () -> plugin.getAdventure().players().sendMessage(component));
      } else {
        String formattedCmd =
            command
                .replace("<player>", nvPlayer.getPlayer().getName())
                .replace("<check_name>", module.getModuleName())
                .replace("<vl>", String.valueOf(vl))
                .replace("<verbose>", verbose);
        Bukkit.getScheduler()
            .runTask(plugin, () -> Bukkit.dispatchCommand(Bukkit.getConsoleSender(), formattedCmd));
      }
    }
  }

  private void sendAlert(AnalysisModule module, int vl, String verbose) {
    final Component message =
        MessageUtil.getMessage(
            Message.ALERTS_FORMAT,
            "player",
            nvPlayer.getPlayer().getName(),
            "check_name",
            module.getModuleName(),
            "vl",
            String.valueOf(vl),
            "verbose",
            verbose);
    Bukkit.getScheduler().runTask(plugin, () -> alertManager.send(message, SignalType.REGULAR));
  }
}


--- src/main/java/club/nezxenka/netvision/service/enforce/internal/executor/CommandExecutor.java ---

package club.nezxenka.netvision.service.enforce.internal.executor;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.engine.api.AnalysisModule;
import club.nezxenka.netvision.service.signal.internal.SignalManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.List;
import java.util.Locale;
import org.bukkit.Bukkit;

public class CommandExecutor {
  private final NetVisionPlayer player;
  private final NetVision plugin;
  private final RecordStorage database;
  private final SignalManager alertManager;

  public CommandExecutor(
      NetVisionPlayer player,
      NetVision plugin,
      RecordStorage database,
      SignalManager alertManager) {
    this.player = player;
    this.plugin = plugin;
    this.database = database;
    this.alertManager = alertManager;
  }

  public void execute(
      AnalysisModule module, int vl, String verbose, List<String> commands, String groupName) {
    for (String cmd : commands) {
      String lower = cmd.toLowerCase(Locale.ROOT);
      if (lower.equals("[alert]"))
        Bukkit.getScheduler()
            .runTask(
                plugin,
                () ->
                    alertManager.send(
                        MessageUtil.getMessage(
                            Message.ALERTS_FORMAT,
                            "player",
                            player.getPlayer().getName(),
                            "check_name",
                            module.getModuleName(),
                            "vl",
                            String.valueOf(vl),
                            "verbose",
                            verbose),
                        SignalType.REGULAR));
      else if (lower.equals("[log]"))
        database.logAlert(player, verbose, module.getModuleName(), vl);
      else if (lower.equals("[reset]")) database.resetViolationLevel(player.getUuid(), groupName);
      else if (lower.startsWith("[broadcast] ")) {
        String msg = cmd.substring("[broadcast] ".length());
        Bukkit.getScheduler()
            .runTask(
                plugin,
                () ->
                    plugin
                        .getAdventure()
                        .players()
                        .sendMessage(
                            MessageUtil.format(
                                msg,
                                "player",
                                player.getPlayer().getName(),
                                "check_name",
                                module.getModuleName(),
                                "vl",
                                String.valueOf(vl),
                                "verbose",
                                verbose)));
      } else {
        String formatted =
            cmd.replace("<player>", player.getPlayer().getName())
                .replace("<check_name>", module.getModuleName())
                .replace("<vl>", String.valueOf(vl))
                .replace("<verbose>", verbose);
        Bukkit.getScheduler()
            .runTask(plugin, () -> Bukkit.dispatchCommand(Bukkit.getConsoleSender(), formatted));
      }
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/enforce/model/EnforcementGroup.java ---

package club.nezxenka.netvision.service.enforce.model;

import club.nezxenka.netvision.engine.api.AnalysisModule;
import java.util.HashSet;
import java.util.List;
import java.util.Locale;
import java.util.NavigableMap;
import java.util.Set;
import lombok.Getter;

@Getter
public class EnforcementGroup {
  private final String groupName;
  private final Set<String> associatedCheckNames;
  private final NavigableMap<Integer, List<String>> actions;

  public EnforcementGroup(
      String groupName, List<String> moduleNames, NavigableMap<Integer, List<String>> actions) {
    this.groupName = groupName;
    this.associatedCheckNames =
        new HashSet<>(moduleNames.stream().map(String::toLowerCase).toList());
    this.actions = actions;
  }

  public boolean isModuleAssociated(AnalysisModule module) {
    String moduleNameLower = module.getModuleName().toLowerCase(Locale.ROOT);
    for (String filter : associatedCheckNames) if (moduleNameLower.contains(filter)) return true;
    return false;
  }
}


--- src/main/java/club/nezxenka/netvision/service/enforce/model/threshold/ThresholdConfig.java ---

package club.nezxenka.netvision.service.enforce.model.threshold;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class ThresholdConfig {
  private int violationLevel;
  private String action;
  private int delay;
}


--- src/main/java/club/nezxenka/netvision/service/hologram/display/builder/HologramLineBuilder.java ---

package club.nezxenka.netvision.service.hologram.display.builder;

import java.util.List;

public class HologramLineBuilder {
  public String buildProbabilityLine(List<Double> probs) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < probs.size(); i++) {
      double p = probs.get(i);
      sb.append(colorCode(p)).append(String.format("%.2f", p));
      if (i < probs.size() - 1) sb.append("&f ");
    }
    return sb.toString();
  }

  public String buildAverageLine(double avg) {
    return "&7AVG: " + colorCode(avg) + String.format("%.5f", avg);
  }

  private String colorCode(double p) {
    if (p > 0.9) return "&c";
    if (p > 0.5) return "&e";
    return "&a";
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/display/formatter/HologramColorFormatter.java ---

package club.nezxenka.netvision.service.hologram.display.formatter;

public class HologramColorFormatter {
  public String format(double probability) {
    if (probability > 0.9) return "&c";
    if (probability > 0.5) return "&e";
    return "&a";
  }

  public String formatAverage(double avg) {
    return "&7AVG: " + format(avg) + String.format("%.5f", avg);
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/display/PlayerHologramRenderer.java ---

package club.nezxenka.netvision.service.hologram.display;

import club.nezxenka.netvision.service.hologram.internal.HologramManager;
import club.nezxenka.netvision.service.hologram.model.ProbabilityHistory;
import eu.decentsoftware.holograms.api.DHAPI;
import eu.decentsoftware.holograms.api.holograms.Hologram;
import java.util.Collections;
import java.util.List;
import org.bukkit.Location;
import org.bukkit.entity.Player;

public class PlayerHologramRenderer {
  private final HologramManager manager;
  private final Player viewer;
  private final Player target;
  private final String hologramId;
  private Hologram hologram;
  private boolean spawned = false;

  public PlayerHologramRenderer(HologramManager manager, Player viewer, Player target) {
    this.manager = manager;
    this.viewer = viewer;
    this.target = target;
    this.hologramId =
        "netvision_"
            + viewer.getUniqueId().toString().replace("-", "")
            + "_"
            + target.getUniqueId().toString().replace("-", "");
  }

  public void spawn() {
    if (spawned || !target.isOnline() || !viewer.isOnline()) return;
    Location loc =
        new Location(
            target.getWorld(),
            target.getLocation().getX(),
            target.getLocation().getY() + 3.0,
            target.getLocation().getZ());
    hologram = DHAPI.createHologram(hologramId, loc, false);
    DHAPI.addHologramLine(hologram, "&c0.00");
    DHAPI.addHologramLine(hologram, "&7AVG: &a0.00000");
    hologram.setDefaultVisibleState(false);
    hologram.setShowPlayer(viewer);
    spawned = true;
  }

  public void update() {
    if (!spawned || hologram == null || !target.isOnline() || !viewer.isOnline()) {
      remove();
      return;
    }
    ProbabilityHistory history = manager.getHistory(target.getUniqueId());
    List<Double> probs = history.getAll();
    double avg = history.getAverage();
    if (probs.isEmpty()) {
      probs = Collections.singletonList(0.0);
      avg = 0.0;
    }
    Location targetLoc = target.getLocation();
    Location holoLoc =
        new Location(
            targetLoc.getWorld(), targetLoc.getX(), targetLoc.getY() + 3.0, targetLoc.getZ());
    DHAPI.moveHologram(hologram, holoLoc);
    DHAPI.setHologramLine(hologram, 0, buildProbLine(probs));
    DHAPI.setHologramLine(hologram, 1, buildAvgLine(avg));
  }

  public void remove() {
    if (!spawned) return;
    if (hologram != null) {
      hologram.delete();
      hologram = null;
    }
    spawned = false;
  }

  private String buildProbLine(List<Double> probs) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < probs.size(); i++) {
      double p = probs.get(i);
      sb.append(colorCode(p)).append(String.format("%.2f", p));
      if (i < probs.size() - 1) sb.append("&f ");
    }
    return sb.toString();
  }

  private String buildAvgLine(double avg) {
    return "&7AVG: " + colorCode(avg) + String.format("%.5f", avg);
  }

  private String colorCode(double p) {
    if (p > 0.9) return "&c";
    if (p > 0.5) return "&e";
    return "&a";
  }

  public boolean isSpawned() {
    return spawned;
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/display/updater/HologramPositionUpdater.java ---

package club.nezxenka.netvision.service.hologram.display.updater;

import eu.decentsoftware.holograms.api.DHAPI;
import eu.decentsoftware.holograms.api.holograms.Hologram;
import org.bukkit.Location;
import org.bukkit.entity.Player;

public class HologramPositionUpdater {
  public void updatePosition(Hologram hologram, Player target) {
    Location loc =
        new Location(
            target.getWorld(),
            target.getLocation().getX(),
            target.getLocation().getY() + 3.0,
            target.getLocation().getZ());
    DHAPI.moveHologram(hologram, loc);
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/internal/HologramManager.java ---

package club.nezxenka.netvision.service.hologram.internal;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.service.hologram.display.PlayerHologramRenderer;
import club.nezxenka.netvision.service.hologram.model.ProbabilityHistory;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.scheduler.BukkitTask;

public class HologramManager {
  private final NetVision plugin;
  private final Map<UUID, Map<UUID, PlayerHologramRenderer>> viewerHolograms =
      new ConcurrentHashMap<>();
  private final Map<UUID, ProbabilityHistory> probabilityHistories = new ConcurrentHashMap<>();
  private BukkitTask updateTask;

  public HologramManager(NetVision plugin) {
    this.plugin = plugin;
    startUpdateTask();
  }

  public void addProbability(UUID playerUuid, double probability) {
    probabilityHistories
        .computeIfAbsent(playerUuid, k -> new ProbabilityHistory())
        .add(probability);
  }

  public ProbabilityHistory getHistory(UUID playerUuid) {
    return probabilityHistories.getOrDefault(playerUuid, new ProbabilityHistory());
  }

  public void enableHologram(Player viewer, Player target) {
    Map<UUID, PlayerHologramRenderer> holograms =
        viewerHolograms.computeIfAbsent(viewer.getUniqueId(), k -> new ConcurrentHashMap<>());
    if (!holograms.containsKey(target.getUniqueId())) {
      PlayerHologramRenderer hologram = new PlayerHologramRenderer(this, viewer, target);
      holograms.put(target.getUniqueId(), hologram);
      hologram.spawn();
    }
  }

  public void enableForAll(Player viewer) {
    for (Player target : Bukkit.getOnlinePlayers()) {
      if (!target.equals(viewer) && !target.hasPermission("netvision.exempt"))
        enableHologram(viewer, target);
    }
  }

  public void disableHologram(Player viewer, Player target) {
    Map<UUID, PlayerHologramRenderer> holograms = viewerHolograms.get(viewer.getUniqueId());
    if (holograms != null) {
      PlayerHologramRenderer hologram = holograms.remove(target.getUniqueId());
      if (hologram != null) hologram.remove();
      if (holograms.isEmpty()) viewerHolograms.remove(viewer.getUniqueId());
    }
  }

  public void disableForAll(Player viewer) {
    Map<UUID, PlayerHologramRenderer> holograms = viewerHolograms.remove(viewer.getUniqueId());
    if (holograms != null) {
      for (PlayerHologramRenderer hologram : holograms.values()) hologram.remove();
    }
  }

  public boolean isEnabled(Player viewer, Player target) {
    Map<UUID, PlayerHologramRenderer> holograms = viewerHolograms.get(viewer.getUniqueId());
    return holograms != null && holograms.containsKey(target.getUniqueId());
  }

  public void handlePlayerQuit(Player player) {
    disableForAll(player);
    probabilityHistories.remove(player.getUniqueId());
    for (Map<UUID, PlayerHologramRenderer> holograms : viewerHolograms.values()) {
      PlayerHologramRenderer hologram = holograms.remove(player.getUniqueId());
      if (hologram != null) hologram.remove();
    }
  }

  private void startUpdateTask() {
    updateTask =
        Bukkit.getScheduler()
            .runTaskTimer(
                plugin,
                () -> {
                  for (Map<UUID, PlayerHologramRenderer> holograms : viewerHolograms.values()) {
                    for (PlayerHologramRenderer hologram : holograms.values()) hologram.update();
                  }
                },
                1L,
                1L);
  }

  public void shutdown() {
    if (updateTask != null) updateTask.cancel();
    for (Map<UUID, PlayerHologramRenderer> holograms : viewerHolograms.values()) {
      for (PlayerHologramRenderer hologram : holograms.values()) hologram.remove();
    }
    viewerHolograms.clear();
    probabilityHistories.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/internal/registry/HologramRegistry.java ---

package club.nezxenka.netvision.service.hologram.internal.registry;

import club.nezxenka.netvision.service.hologram.display.PlayerHologramRenderer;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class HologramRegistry {
  private final Map<UUID, Map<UUID, PlayerHologramRenderer>> viewerHolograms =
      new ConcurrentHashMap<>();

  public Map<UUID, PlayerHologramRenderer> getOrCreate(UUID viewer) {
    return viewerHolograms.computeIfAbsent(viewer, k -> new ConcurrentHashMap<>());
  }

  public Map<UUID, PlayerHologramRenderer> get(UUID viewer) {
    return viewerHolograms.get(viewer);
  }

  public Map<UUID, PlayerHologramRenderer> remove(UUID viewer) {
    return viewerHolograms.remove(viewer);
  }

  public void forEachTarget(java.util.function.Consumer<PlayerHologramRenderer> action) {
    for (Map<UUID, PlayerHologramRenderer> map : viewerHolograms.values())
      for (PlayerHologramRenderer r : map.values()) action.accept(r);
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/model/cache/ProbabilityCache.java ---

package club.nezxenka.netvision.service.hologram.model.cache;

import club.nezxenka.netvision.service.hologram.model.ProbabilityHistory;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class ProbabilityCache {
  private final Map<UUID, ProbabilityHistory> cache = new ConcurrentHashMap<>();

  public void add(UUID uuid, double probability) {
    cache.computeIfAbsent(uuid, k -> new ProbabilityHistory()).add(probability);
  }

  public ProbabilityHistory get(UUID uuid) {
    return cache.getOrDefault(uuid, new ProbabilityHistory());
  }

  public void remove(UUID uuid) {
    cache.remove(uuid);
  }
}


--- src/main/java/club/nezxenka/netvision/service/hologram/model/ProbabilityHistory.java ---

package club.nezxenka.netvision.service.hologram.model;

import java.util.ArrayList;
import java.util.List;

public class ProbabilityHistory {
  private final Double[] probabilities = new Double[5];
  private int currentIndex = 0;
  private int count = 0;

  public void add(double probability) {
    probabilities[currentIndex] = probability;
    currentIndex = (currentIndex + 1) % 5;
    if (count < 5) count++;
  }

  public List<Double> getAll() {
    List<Double> result = new ArrayList<>();
    if (count == 0) return result;
    for (int i = 0; i < count; i++) {
      int index = (currentIndex - 1 - i + 50) % 5;
      if (probabilities[index] != null) result.add(probabilities[index]);
    }
    return result;
  }

  public double getAverage() {
    if (count == 0) return 0.0;
    double sum = 0.0;
    for (int i = 0; i < count; i++) {
      if (probabilities[i] != null) sum += probabilities[i];
    }
    return sum / count;
  }

  public boolean isEmpty() {
    return count == 0;
  }
}


--- src/main/java/club/nezxenka/netvision/service/signal/internal/dispatch/SignalDispatchPolicy.java ---

package club.nezxenka.netvision.service.signal.internal.dispatch;

import club.nezxenka.netvision.service.signal.model.SignalType;
import java.util.UUID;
import net.kyori.adventure.text.Component;

public interface SignalDispatchPolicy {
  boolean shouldDeliver(SignalType type, UUID playerUuid);

  Component transformMessage(Component original, SignalType type);
}


--- src/main/java/club/nezxenka/netvision/service/signal/internal/SignalManager.java ---

package club.nezxenka.netvision.service.signal.internal;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.config.ConfigManager;
import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.service.signal.model.SignalType;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import java.util.EnumMap;
import java.util.EnumSet;
import java.util.Map;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.CopyOnWriteArraySet;
import lombok.Getter;
import net.kyori.adventure.audience.Audience;
import net.kyori.adventure.platform.bukkit.BukkitAudiences;
import net.kyori.adventure.text.Component;
import org.bukkit.Bukkit;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;

public class SignalManager {

  private final ConfigManager configManager;
  private final LocaleManager localeManager;
  private final BukkitAudiences adventure;
  private final Map<SignalType, Set<UUID>> playersWithAlerts = new EnumMap<>(SignalType.class);
  private final Set<SignalType> consoleAlertsEnabled = EnumSet.allOf(SignalType.class);
  private boolean logToConsole;

  @Getter private String alertFormat;

  @Getter private String brandAlertFormat;

  private volatile club.nezxenka.netvision.service.bridge.alert.CrossServerPublisher
      crossServerPublisher;

  public void setCrossServerPublisher(
      club.nezxenka.netvision.service.bridge.alert.CrossServerPublisher publisher) {
    this.crossServerPublisher = publisher;
  }

  public SignalManager(
      NetVision plugin,
      ConfigManager configManager,
      LocaleManager localeManager,
      BukkitAudiences adventure) {
    this.configManager = configManager;
    this.localeManager = localeManager;
    this.adventure = adventure;
    for (SignalType type : SignalType.values())
      playersWithAlerts.put(type, new CopyOnWriteArraySet<>());
    reload();
  }

  public void reload() {
    this.logToConsole = configManager.getConfig().getBoolean("alerts.print-to-console", true);
    this.alertFormat = localeManager.getRawMessage(Message.ALERTS_FORMAT);
    this.brandAlertFormat = localeManager.getRawMessage(Message.BRAND_NOTIFICATION);
  }

  public void toggle(Player player, SignalType type, boolean silent) {
    Set<UUID> playersSet = playersWithAlerts.get(type);
    UUID uuid = player.getUniqueId();
    if (playersSet.contains(uuid)) {
      playersSet.remove(uuid);
      if (!silent) adventure(player).sendMessage(MessageUtil.getMessage(type.getDisabledMessage()));
    } else {
      playersSet.add(uuid);
      if (!silent) adventure(player).sendMessage(MessageUtil.getMessage(type.getEnabledMessage()));
    }
  }

  public void send(Component component, SignalType type) {
    deliver(component, type);
    club.nezxenka.netvision.service.bridge.alert.CrossServerPublisher publisher =
        this.crossServerPublisher;
    if (publisher != null) publisher.publish(type, component);
  }

  public void deliver(Component component, SignalType type) {
    Set<UUID> playersSet = playersWithAlerts.get(type);
    String permission = type.getPermission();
    for (UUID uuid : playersSet) {
      Player p = Bukkit.getPlayer(uuid);
      if (p != null && p.hasPermission(permission)) adventure(p).sendMessage(component);
    }
    if (logToConsole && consoleAlertsEnabled.contains(type))
      adventure(Bukkit.getConsoleSender()).sendMessage(component);
  }

  public boolean hasAlertsEnabled(Player player, SignalType type) {
    return playersWithAlerts.get(type).contains(player.getUniqueId());
  }

  public boolean isConsoleAlertsEnabled(SignalType type) {
    return consoleAlertsEnabled.contains(type);
  }

  public void toggleConsoleAlerts(SignalType type) {
    if (consoleAlertsEnabled.contains(type)) consoleAlertsEnabled.remove(type);
    else consoleAlertsEnabled.add(type);
  }

  public void handlePlayerQuit(Player player) {
    UUID uuid = player.getUniqueId();
    for (Set<UUID> players : playersWithAlerts.values()) players.remove(uuid);
  }

  private Audience adventure(Player player) {
    return adventure.player(player);
  }

  private Audience adventure(CommandSender sender) {
    return adventure.sender(sender);
  }
}


--- src/main/java/club/nezxenka/netvision/service/signal/internal/toggle/SignalToggleHandler.java ---

package club.nezxenka.netvision.service.signal.internal.toggle;

import club.nezxenka.netvision.service.signal.model.SignalType;
import java.util.Set;
import java.util.UUID;
import org.bukkit.entity.Player;

public class SignalToggleHandler {
  public boolean processToggle(Set<UUID> playerSet, Player player, SignalType type) {
    UUID uuid = player.getUniqueId();
    if (playerSet.contains(uuid)) {
      playerSet.remove(uuid);
      return false;
    } else {
      playerSet.add(uuid);
      return true;
    }
  }
}


--- src/main/java/club/nezxenka/netvision/service/signal/model/config/SignalConfiguration.java ---

package club.nezxenka.netvision.service.signal.model.config;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class SignalConfiguration {
  private boolean logToConsole;
  private String alertFormat;
  private String brandAlertFormat;
}


--- src/main/java/club/nezxenka/netvision/service/signal/model/SignalType.java ---

package club.nezxenka.netvision.service.signal.model;

import club.nezxenka.netvision.util.message.Message;
import lombok.Getter;

@Getter
public enum SignalType {
  REGULAR("netvision.alerts", Message.ALERTS_ENABLED, Message.ALERTS_DISABLED),
  BRAND("netvision.brand", Message.BRAND_ALERTS_ENABLED, Message.BRAND_ALERTS_DISABLED),
  SUSPICIOUS(
      "netvision.suspicious.alerts",
      Message.SUSPICIOUS_ALERTS_ENABLED,
      Message.SUSPICIOUS_ALERTS_DISABLED);

  private final String permission;
  private final Message enabledMessage;
  private final Message disabledMessage;

  SignalType(String permission, Message enabledMessage, Message disabledMessage) {
    this.permission = permission;
    this.enabledMessage = enabledMessage;
    this.disabledMessage = disabledMessage;
  }
}


--- src/main/java/club/nezxenka/netvision/util/chat/ChatUtil.java ---

package club.nezxenka.netvision.util.chat;

import java.util.regex.Pattern;
import org.jetbrains.annotations.Contract;
import org.jetbrains.annotations.NotNull;
import org.jetbrains.annotations.Nullable;

public class ChatUtil {
  private static final Pattern STRIP_COLOR_PATTERN =
      Pattern.compile("(?i)" + '§' + "[0-9A-FK-ORX]");

  public static @NotNull String translateAlternateColorCodes(
      char altColorChar, @NotNull String textToTranslate) {
    char[] b = textToTranslate.toCharArray();
    for (int i = 0; i < b.length - 1; ++i) {
      if (b[i] == altColorChar && "0123456789AaBbCcDdEeFfKkLlMmNnOoRrXx".indexOf(b[i + 1]) > -1) {
        b[i] = 167;
        b[i + 1] = Character.toLowerCase(b[i + 1]);
      }
    }
    return new String(b);
  }

  @Contract("!null -> !null; null -> null")
  public static @Nullable String stripColor(@Nullable String input) {
    return input == null ? null : STRIP_COLOR_PATTERN.matcher(input).replaceAll("");
  }
}


--- src/main/java/club/nezxenka/netvision/util/chat/color/ColorTranslator.java ---

package club.nezxenka.netvision.util.chat.color;

public class ColorTranslator {
  private static final String COLOR_CODES = "0123456789AaBbCcDdEeFfKkLlMmNnOoRrXx";

  public char[] translate(char altChar, char[] chars) {
    char[] result = new char[chars.length];
    System.arraycopy(chars, 0, result, 0, chars.length);
    for (int i = 0; i < result.length - 1; i++) {
      if (result[i] == altChar && COLOR_CODES.indexOf(result[i + 1]) > -1) {
        result[i] = 167;
        result[i + 1] = Character.toLowerCase(result[i + 1]);
      }
    }
    return result;
  }
}


--- src/main/java/club/nezxenka/netvision/util/chat/stripper/ColorStripper.java ---

package club.nezxenka.netvision.util.chat.stripper;

import java.util.regex.Pattern;

public class ColorStripper {
  private static final Pattern PATTERN = Pattern.compile("(?i)" + '\u00A7' + "[0-9A-FK-ORX]");

  public String strip(String input) {
    return input == null ? null : PATTERN.matcher(input).replaceAll("");
  }
}


--- src/main/java/club/nezxenka/netvision/util/collection/map/ConcurrentIndex.java ---

package club.nezxenka.netvision.util.collection.map;

import java.util.concurrent.ConcurrentHashMap;
import java.util.function.Function;

public class ConcurrentIndex<K, V> {
  private final ConcurrentHashMap<K, V> map = new ConcurrentHashMap<>();

  public V computeIfAbsent(K key, Function<K, V> factory) {
    return map.computeIfAbsent(key, factory);
  }

  public V get(K key) {
    return map.get(key);
  }

  public void put(K key, V value) {
    map.put(key, value);
  }

  public void remove(K key) {
    map.remove(key);
  }

  public int size() {
    return map.size();
  }
}


--- src/main/java/club/nezxenka/netvision/util/collection/Pair.java ---

package club.nezxenka.netvision.util.collection;

public record Pair<A, B>(A first, B second) {}


--- src/main/java/club/nezxenka/netvision/util/collection/queue/BoundedFifoQueue.java ---

package club.nezxenka.netvision.util.collection.queue;

import java.util.ArrayDeque;

public class BoundedFifoQueue<T> {
  private final ArrayDeque<T> deque = new ArrayDeque<>();
  private final int maxSize;

  public BoundedFifoQueue(int maxSize) {
    this.maxSize = maxSize;
  }

  public void add(T item) {
    deque.addLast(item);
    if (deque.size() > maxSize) deque.removeFirst();
  }

  public T poll() {
    return deque.pollFirst();
  }

  public T peek() {
    return deque.peekFirst();
  }

  public int size() {
    return deque.size();
  }

  public void clear() {
    deque.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/util/collection/RunningMode.java ---

package club.nezxenka.netvision.util.collection;

import it.unimi.dsi.fastutil.doubles.Double2IntMap;
import it.unimi.dsi.fastutil.doubles.Double2IntOpenHashMap;
import java.util.Queue;
import java.util.concurrent.ArrayBlockingQueue;
import lombok.Getter;

public class RunningMode {
  private static final double threshold = 1e-3;
  private final Queue<Double> addList;
  private final Double2IntMap popularityMap = new Double2IntOpenHashMap();
  @Getter private final int maxSize;

  public RunningMode(int maxSize) {
    if (maxSize == 0) throw new IllegalArgumentException("There's no mode to a size 0 list!");
    this.addList = new ArrayBlockingQueue<>(maxSize);
    this.maxSize = maxSize;
  }

  public int size() {
    return addList.size();
  }

  public void add(double value) {
    pop();
    for (Double2IntMap.Entry entry : popularityMap.double2IntEntrySet()) {
      if (Math.abs(entry.getDoubleKey() - value) < threshold) {
        entry.setValue(entry.getIntValue() + 1);
        addList.add(entry.getDoubleKey());
        return;
      }
    }
    popularityMap.put(value, 1);
    addList.add(value);
  }

  private void pop() {
    if (addList.size() >= maxSize) {
      double type = addList.poll();
      int popularity = popularityMap.get(type);
      if (popularity == 1) popularityMap.remove(type);
      else popularityMap.put(type, popularity - 1);
    }
  }

  public Pair<Double, Integer> getMode() {
    int max = 0;
    Double mostPopular = null;
    for (Double2IntMap.Entry entry : popularityMap.double2IntEntrySet()) {
      if (entry.getIntValue() > max) {
        max = entry.getIntValue();
        mostPopular = entry.getDoubleKey();
      }
    }
    return new Pair<>(mostPopular, max);
  }
}


--- src/main/java/club/nezxenka/netvision/util/latency/async/AsyncTaskRunner.java ---

package club.nezxenka.netvision.util.latency.async;

import com.github.retrooper.packetevents.netty.channel.ChannelHelper;
import io.netty.channel.Channel;

public class AsyncTaskRunner {
  public void runInEventLoop(Channel channel, Runnable task) {
    ChannelHelper.runInEventLoop(channel, task);
  }

  public void runDirect(Runnable task) {
    task.run();
  }
}


--- src/main/java/club/nezxenka/netvision/util/latency/ILatencyUtils.java ---

package club.nezxenka.netvision.util.latency;

public interface ILatencyUtils {
  void addRealTimeTask(int transaction, Runnable runnable);

  void addRealTimeTaskAsync(int transaction, Runnable runnable);

  void handleNettySyncTransaction(int receivedTransactionId);
}


--- src/main/java/club/nezxenka/netvision/util/latency/LatencyUtils.java ---

package club.nezxenka.netvision.util.latency;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.actor.model.NetVisionPlayer;
import club.nezxenka.netvision.util.message.Message;
import club.nezxenka.netvision.util.message.MessageUtil;
import com.github.retrooper.packetevents.netty.channel.ChannelHelper;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Iterator;

public class LatencyUtils implements ILatencyUtils {
  private record TransactionTask(int transactionId, Runnable task) {}

  private final ArrayDeque<TransactionTask> transactionMap = new ArrayDeque<>();
  private final NetVisionPlayer player;
  private final NetVision plugin;
  private final ArrayList<Runnable> tasksToRun = new ArrayList<>();

  public LatencyUtils(NetVisionPlayer player, NetVision plugin) {
    this.player = player;
    this.plugin = plugin;
  }

  @Override
  public void addRealTimeTask(int transaction, Runnable runnable) {
    addRealTimeTaskInternal(transaction, false, runnable);
  }

  @Override
  public void addRealTimeTaskAsync(int transaction, Runnable runnable) {
    addRealTimeTaskInternal(transaction, true, runnable);
  }

  private void addRealTimeTaskInternal(int transactionId, boolean async, Runnable runnable) {
    if (player.getLastTransactionReceived().get() >= transactionId) {
      if (async) ChannelHelper.runInEventLoop(player.getUser().getChannel(), runnable);
      else runnable.run();
      return;
    }
    synchronized (transactionMap) {
      transactionMap.add(new TransactionTask(transactionId, runnable));
    }
  }

  @Override
  public void handleNettySyncTransaction(int receivedTransactionId) {
    synchronized (transactionMap) {
      tasksToRun.clear();
      Iterator<TransactionTask> iterator = transactionMap.iterator();
      while (iterator.hasNext()) {
        TransactionTask taskEntry = iterator.next();
        int taskTransactionId = taskEntry.transactionId();
        if (receivedTransactionId + 1 < taskTransactionId) break;
        if (receivedTransactionId == taskTransactionId - 1) continue;
        tasksToRun.add(taskEntry.task());
        iterator.remove();
      }
      for (Runnable runnable : tasksToRun) {
        try {
          runnable.run();
        } catch (Exception e) {
          plugin
              .getLogger()
              .severe(
                  "An error occurred when running transactions for player: "
                      + player.getUser().getName());
          e.printStackTrace();
          player.disconnect(MessageUtil.getMessage(Message.INTERNAL_ERROR));
        }
      }
    }
  }
}


--- src/main/java/club/nezxenka/netvision/util/latency/task/TransactionTaskEntry.java ---

package club.nezxenka.netvision.util.latency.task;

public class TransactionTaskEntry implements Comparable<TransactionTaskEntry> {
  private final int transactionId;
  private final Runnable task;

  public TransactionTaskEntry(int transactionId, Runnable task) {
    this.transactionId = transactionId;
    this.task = task;
  }

  public int transactionId() {
    return transactionId;
  }

  public Runnable task() {
    return task;
  }

  @Override
  public int compareTo(TransactionTaskEntry o) {
    return Integer.compare(this.transactionId, o.transactionId);
  }
}


--- src/main/java/club/nezxenka/netvision/util/math/gcd/GcdComputer.java ---

package club.nezxenka.netvision.util.math.gcd;

public class GcdComputer {
  private static final double MINIMUM = ((Math.pow(0.2f, 3) * 8) * 0.15) - 1e-3;

  public double compute(double a, double b) {
    if (a == 0) return 0;
    if (a < b) {
      double t = a;
      a = b;
      b = t;
    }
    while (b > MINIMUM) {
      double t = a - (Math.floor(a / b) * b);
      a = b;
      b = t;
    }
    return a;
  }
}


--- src/main/java/club/nezxenka/netvision/util/math/NetVisionMath.java ---

package club.nezxenka.netvision.util.math;

import lombok.experimental.UtilityClass;

@UtilityClass
public class NetVisionMath {
  public static final double MINIMUM_DIVISOR = ((Math.pow(0.2f, 3) * 8) * 0.15) - 1e-3;

  public static double gcd(double a, double b) {
    if (a == 0) return 0;
    if (a < b) {
      double temp = a;
      a = b;
      b = temp;
    }
    while (b > MINIMUM_DIVISOR) {
      double temp = a - (Math.floor(a / b) * b);
      a = b;
      b = temp;
    }
    return a;
  }
}


--- src/main/java/club/nezxenka/netvision/util/math/vector/VectorUtils.java ---

package club.nezxenka.netvision.util.math.vector;

import com.github.retrooper.packetevents.util.Vector3d;

public class VectorUtils {
  public double distanceSquared(Vector3d a, Vector3d b) {
    return a.distanceSquared(b);
  }

  public boolean isNear(double actual, double expected, double epsilon) {
    return Math.abs(actual - expected) < epsilon;
  }

  public double epsilon() {
    return 1.0E-7;
  }
}


--- src/main/java/club/nezxenka/netvision/util/message/formatter/MiniMessageFormatter.java ---

package club.nezxenka.netvision.util.message.formatter;

import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.minimessage.MiniMessage;
import net.kyori.adventure.text.minimessage.tag.resolver.Placeholder;
import net.kyori.adventure.text.minimessage.tag.resolver.TagResolver;

public class MiniMessageFormatter {
  private static final MiniMessage MINI = MiniMessage.miniMessage();

  public Component format(String template, String... placeholders) {
    TagResolver.Builder builder = TagResolver.builder();
    if (placeholders.length > 0 && placeholders.length % 2 == 0) {
      for (int i = 0; i < placeholders.length; i += 2)
        builder.resolver(
            Placeholder.component(placeholders[i], Component.text(placeholders[i + 1])));
    }
    return MINI.deserialize(template, builder.build());
  }
}


--- src/main/java/club/nezxenka/netvision/util/message/key/MessageKeyRegistry.java ---

package club.nezxenka.netvision.util.message.key;

import club.nezxenka.netvision.util.message.Message;
import java.util.EnumMap;
import java.util.Map;

public class MessageKeyRegistry {
  private final Map<Message, String> cache = new EnumMap<>(Message.class);

  public void cache(Message key, String raw) {
    cache.put(key, raw);
  }

  public String get(Message key) {
    return cache.get(key);
  }

  public void clear() {
    cache.clear();
  }
}


--- src/main/java/club/nezxenka/netvision/util/message/Message.java ---

package club.nezxenka.netvision.util.message;

import lombok.Getter;

@Getter
public enum Message {
  PREFIX("prefix"),
  ALERTS_ENABLED("alerts-enabled"),
  ALERTS_DISABLED("alerts-disabled"),
  ALERTS_FORMAT("alerts-format"),
  PLAYER_NOT_FOUND("player-not-found"),
  RUN_AS_PLAYER("run-as-player"),
  RELOAD_START("reload-start"),
  RELOAD_SUCCESS("reload-success"),
  BRAND_ALERTS_ENABLED("brand.alerts-enabled"),
  BRAND_ALERTS_DISABLED("brand.alerts-disabled"),
  BRAND_NOTIFICATION("brand.notification"),
  BRAND_DISCONNECT_FORGE("brand.disconnect-forge"),
  CROSS_SERVER_ALERT_PREFIX("cross-server.alert-prefix"),
  FP_SUCCESS("falsepositive.success"),
  FP_FAIL("falsepositive.fail"),
  FP_NO_DATA("falsepositive.no-data"),
  PROB_ENABLED("prob.enabled"),
  PROB_DISABLED("prob.disabled"),
  PROB_NO_DATA("prob.no-data"),
  PROB_NO_AICHECK("prob.no-aicheck"),
  PROB_FORMAT_LABEL_PROB("prob.format.label-prob"),
  PROB_FORMAT_LABEL_BUFFER("prob.format.label-buffer"),
  PROB_FORMAT_LABEL_PING("prob.format.label-ping"),
  PROB_FORMAT_SEPARATOR("prob.format.separator"),
  PROB_FORMAT_SUFFIX_PING("prob.format.suffix-ping"),
  PROFILE_NO_DATA("profile.no-data"),
  PROFILE_LINES("profile.lines"),
  HISTORY_DISABLED("history.disabled"),
  HISTORY_HEADER("history.header"),
  HISTORY_ENTRY("history.entry"),
  HISTORY_NO_VIOLATIONS("history.no-violations"),
  LOGS_HEADER("logs.header"),
  LOGS_ENTRY("logs.entry"),
  LOGS_NO_VIOLATIONS("logs.no-violations"),
  LOGS_INVALID_TIME("logs.invalid-time"),
  PUNISH_RESET_SUCCESS("punish.reset-success"),
  SUSPICIOUS_ALERTS_ENABLED("suspicious.alerts-enabled"),
  SUSPICIOUS_ALERTS_DISABLED("suspicious.alerts-disabled"),
  SUSPICIOUS_ALERT_TRIGGERED("suspicious.alert-triggered"),
  SUSPICIOUS_LIST_EMPTY("suspicious.list-empty"),
  SUSPICIOUS_LIST_HEADER("suspicious.list-header"),
  SUSPICIOUS_LIST_ENTRY("suspicious.list-entry"),
  SUSPICIOUS_TOP_NONE("suspicious.top-none"),
  SUSPICIOUS_TOP_PLAYER("suspicious.top-player"),
  STATS_LINES("stats.lines"),
  HELP_MESSAGE("help"),
  INTERNAL_ERROR("internal.error"),
  TIME_AGO("time.ago"),
  TIME_DAYS("time.days"),
  TIME_HOURS("time.hours"),
  TIME_MINUTES("time.minutes"),
  TIME_SECONDS("time.seconds");

  private final String path;

  Message(String path) {
    this.path = path;
  }
}


--- src/main/java/club/nezxenka/netvision/util/message/MessageUtil.java ---

package club.nezxenka.netvision.util.message;

import club.nezxenka.netvision.core.locale.LocaleManager;
import java.util.List;
import java.util.stream.Collectors;
import net.kyori.adventure.platform.bukkit.BukkitAudiences;
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.minimessage.MiniMessage;
import net.kyori.adventure.text.minimessage.tag.resolver.Placeholder;
import net.kyori.adventure.text.minimessage.tag.resolver.TagResolver;
import org.bukkit.command.CommandSender;

public class MessageUtil {
  private static final MiniMessage miniMessage = MiniMessage.miniMessage();
  private static LocaleManager localeManager;
  private static BukkitAudiences adventure;

  public static void init(LocaleManager localeManager, BukkitAudiences adventure) {
    MessageUtil.localeManager = localeManager;
    MessageUtil.adventure = adventure;
  }

  public static Component format(String message, String... placeholders) {
    String processedMessage =
        message.replace("<prefix>", localeManager.getRawMessage(Message.PREFIX));
    TagResolver.Builder resolverBuilder = TagResolver.builder();
    if (placeholders.length > 0) {
      if (placeholders.length % 2 != 0)
        System.err.println("Invalid placeholders count for message: " + message);
      else
        for (int i = 0; i < placeholders.length; i += 2)
          resolverBuilder.resolver(
              Placeholder.component(placeholders[i], Component.text(placeholders[i + 1])));
    }
    return miniMessage.deserialize(processedMessage, resolverBuilder.build());
  }

  public static void sendMessage(CommandSender sender, Message key, String... placeholders) {
    adventure.sender(sender).sendMessage(getMessage(key, placeholders));
  }

  public static void sendMessageList(CommandSender sender, Message key, String... placeholders) {
    getMessageList(key, placeholders).forEach(line -> adventure.sender(sender).sendMessage(line));
  }

  public static Component getMessage(Message key, String... placeholders) {
    String rawMessage = localeManager.getRawMessage(key);
    return format(rawMessage, placeholders);
  }

  public static List<Component> getMessageList(Message key, String... placeholders) {
    return localeManager.getRawMessageList(key).stream()
        .map(line -> format(line, placeholders))
        .collect(Collectors.toList());
  }
}


--- src/main/java/club/nezxenka/netvision/util/message/sender/ComponentSender.java ---

package club.nezxenka.netvision.util.message.sender;

import net.kyori.adventure.platform.bukkit.BukkitAudiences;
import net.kyori.adventure.text.Component;
import org.bukkit.command.CommandSender;

public class ComponentSender {
  private final BukkitAudiences adventure;

  public ComponentSender(BukkitAudiences adventure) {
    this.adventure = adventure;
  }

  public void send(CommandSender recipient, Component message) {
    adventure.sender(recipient).sendMessage(message);
  }
}


--- src/main/java/club/nezxenka/netvision/util/rotation/calculator/RotationDeltaCalculator.java ---

package club.nezxenka.netvision.util.rotation.calculator;

public class RotationDeltaCalculator {
  public float yawDelta(float current, float previous) {
    return current - previous;
  }

  public float pitchDelta(float current, float previous) {
    return current - previous;
  }

  public float normalizeYaw(float yaw) {
    yaw = yaw % 360;
    if (yaw > 180) yaw -= 360;
    if (yaw < -180) yaw += 360;
    return yaw;
  }
}


--- src/main/java/club/nezxenka/netvision/util/rotation/HeadRotation.java ---

package club.nezxenka.netvision.util.rotation;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class HeadRotation {
  float yaw, pitch;
}


--- src/main/java/club/nezxenka/netvision/util/rotation/PacketStateData.java ---

package club.nezxenka.netvision.util.rotation;

import com.github.retrooper.packetevents.util.Vector3d;
import lombok.Getter;

@Getter
public class PacketStateData {
  public boolean packetPlayerOnGround = false;
  public boolean lastPacketWasTeleport = false;
  public boolean lastPacketWasServerRotation = false;
  public boolean lastPacketWasOnePointSeventeenDuplicate = false;
  public boolean ignoreDuplicatePacketRotation = true;
  public Vector3d lastClaimedPosition = new Vector3d(0, 0, 0);
}


--- src/main/java/club/nezxenka/netvision/util/rotation/RotationUpdate.java ---

package club.nezxenka.netvision.util.rotation;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public final class RotationUpdate {
  private HeadRotation from;
  private HeadRotation to;
  private float deltaYaw;
  private float deltaPitch;

  public RotationUpdate(HeadRotation from, HeadRotation to, float deltaYaw, float deltaPitch) {
    this.from = from;
    this.to = to;
    this.deltaYaw = deltaYaw;
    this.deltaPitch = deltaPitch;
  }
}


--- src/main/java/club/nezxenka/netvision/util/rotation/snapshot/RotationSnapshot.java ---

package club.nezxenka.netvision.util.rotation.snapshot;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class RotationSnapshot {
  private float fromYaw;
  private float fromPitch;
  private float toYaw;
  private float toPitch;
  private float deltaYaw;
  private float deltaPitch;
}


--- src/main/java/club/nezxenka/netvision/util/time/ago/TimeAgoFormatter.java ---

package club.nezxenka.netvision.util.time.ago;

import java.time.Instant;
import java.util.concurrent.TimeUnit;

public class TimeAgoFormatter {

  public String format(
      Instant instant,
      String agoSuffix,
      String dayUnit,
      String hourUnit,
      String minUnit,
      String secUnit) {
    long millis = System.currentTimeMillis() - instant.toEpochMilli();
    long days = TimeUnit.MILLISECONDS.toDays(millis);
    if (days > 0) return days + dayUnit + agoSuffix;
    long hours = TimeUnit.MILLISECONDS.toHours(millis);
    if (hours > 0) return hours + hourUnit + agoSuffix;
    long minutes = TimeUnit.MILLISECONDS.toMinutes(millis);
    if (minutes > 0) return minutes + minUnit + agoSuffix;
    return TimeUnit.MILLISECONDS.toSeconds(millis) + secUnit + agoSuffix;
  }
}


--- src/main/java/club/nezxenka/netvision/util/time/duration/DurationFormatter.java ---

package club.nezxenka.netvision.util.time.duration;

import java.util.concurrent.TimeUnit;

public class DurationFormatter {
  public String format(
      long millis, String dayUnit, String hourUnit, String minUnit, String secUnit) {
    if (millis < 0) return "0" + secUnit;
    long days = TimeUnit.MILLISECONDS.toDays(millis);
    millis -= TimeUnit.DAYS.toMillis(days);
    long hours = TimeUnit.MILLISECONDS.toHours(millis);
    millis -= TimeUnit.HOURS.toMillis(hours);
    long minutes = TimeUnit.MILLISECONDS.toMinutes(millis);
    millis -= TimeUnit.MINUTES.toMillis(minutes);
    long seconds = TimeUnit.MILLISECONDS.toSeconds(millis);
    StringBuilder sb = new StringBuilder();
    if (days > 0) sb.append(days).append(dayUnit).append(" ");
    if (hours > 0) sb.append(hours).append(hourUnit).append(" ");
    if (minutes > 0) sb.append(minutes).append(minUnit).append(" ");
    if (sb.length() == 0 || seconds > 0) sb.append(seconds).append(secUnit);
    return sb.toString().trim();
  }
}


--- src/main/java/club/nezxenka/netvision/util/time/TimeUtil.java ---

package club.nezxenka.netvision.util.time;

import club.nezxenka.netvision.core.locale.LocaleManager;
import club.nezxenka.netvision.util.message.Message;
import java.time.Instant;
import java.util.concurrent.TimeUnit;
import lombok.experimental.UtilityClass;

@UtilityClass
public class TimeUtil {

  public String formatDuration(long millis, LocaleManager lm) {
    if (millis < 0) return "0" + lm.getRawMessage(Message.TIME_SECONDS);
    String d = lm.getRawMessage(Message.TIME_DAYS);
    String h = lm.getRawMessage(Message.TIME_HOURS);
    String m = lm.getRawMessage(Message.TIME_MINUTES);
    String s = lm.getRawMessage(Message.TIME_SECONDS);
    long days = TimeUnit.MILLISECONDS.toDays(millis);
    millis -= TimeUnit.DAYS.toMillis(days);
    long hours = TimeUnit.MILLISECONDS.toHours(millis);
    millis -= TimeUnit.HOURS.toMillis(hours);
    long minutes = TimeUnit.MILLISECONDS.toMinutes(millis);
    millis -= TimeUnit.MINUTES.toMillis(minutes);
    long seconds = TimeUnit.MILLISECONDS.toSeconds(millis);
    StringBuilder sb = new StringBuilder();
    if (days > 0) sb.append(days).append(d).append(" ");
    if (hours > 0) sb.append(hours).append(h).append(" ");
    if (minutes > 0) sb.append(minutes).append(m).append(" ");
    if (sb.length() == 0 || seconds > 0) sb.append(seconds).append(s);
    return sb.toString().trim();
  }

  public String formatTimeAgo(Instant instant, LocaleManager lm) {
    String ago = lm.getRawMessage(Message.TIME_AGO);
    long durationMillis = System.currentTimeMillis() - instant.toEpochMilli();
    long days = TimeUnit.MILLISECONDS.toDays(durationMillis);
    if (days > 0) return days + lm.getRawMessage(Message.TIME_DAYS) + ago;
    long hours = TimeUnit.MILLISECONDS.toHours(durationMillis);
    if (hours > 0) return (hours + lm.getRawMessage(Message.TIME_HOURS) + ago);
    long minutes = TimeUnit.MILLISECONDS.toMinutes(durationMillis);
    if (minutes > 0) return (minutes + lm.getRawMessage(Message.TIME_MINUTES) + ago);
    return (TimeUnit.MILLISECONDS.toSeconds(durationMillis)
        + lm.getRawMessage(Message.TIME_SECONDS)
        + ago);
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/chickencoop/ChickenCoopMenu.java ---

package club.nezxenka.netvision.visual.menu.coop;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.storage.model.PlayerMenuData;
import club.nezxenka.netvision.visual.menu.coop.display.WoolItemFactory;
import club.nezxenka.netvision.visual.menu.coop.model.PlayerRiskData;
import java.util.Iterator;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.stream.Collectors;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.inventory.Inventory;

public class ChickenCoopMenu {
  private static final int MENU_SIZE = 54;
  private static final String MENU_TITLE = "Курятник";
  private static final int MAX_PROBABILITIES = 10;
  private final NetVision plugin;
  private final LinkedHashMap<UUID, PlayerRiskData> playerRisks = new LinkedHashMap<>();

  public ChickenCoopMenu(NetVision plugin) {
    this.plugin = plugin;
    loadFromDatabase();
  }

  private void loadFromDatabase() {
    Bukkit.getScheduler()
        .runTaskAsynchronously(
            plugin,
            () -> {
              Map<UUID, PlayerMenuData> data =
                  plugin.getDatabaseManager().getDatabase().getAllOnlinePlayerMenuData();
              Bukkit.getScheduler()
                  .runTask(
                      plugin,
                      () -> {
                        for (PlayerMenuData menuData : data.values()) {
                          PlayerRiskData riskData =
                              new PlayerRiskData(menuData.getUuid(), menuData.getPlayerName());
                          for (Double prob : menuData.getProbabilities())
                            riskData.addProbability(prob);
                          playerRisks.put(menuData.getUuid(), riskData);
                        }
                      });
            });
  }

  public void addOrUpdatePlayer(UUID uuid, String playerName, double probability) {
    PlayerRiskData data = playerRisks.get(uuid);
    if (data == null) {
      data = new PlayerRiskData(uuid, playerName);
      playerRisks.put(uuid, data);
    }
    data.addProbability(probability);
    Bukkit.getScheduler()
        .runTaskAsynchronously(
            plugin,
            () ->
                plugin
                    .getDatabaseManager()
                    .getDatabase()
                    .saveProbability(uuid, playerName, probability));
    if (playerRisks.size() > MENU_SIZE) {
      Iterator<UUID> iterator = playerRisks.keySet().iterator();
      if (iterator.hasNext()) {
        iterator.next();
        iterator.remove();
      }
    }
  }

  public void removePlayer(UUID uuid) {
    playerRisks.remove(uuid);
  }

  public void restorePlayer(UUID uuid, String playerName) {
    if (playerRisks.containsKey(uuid)) return;
    Bukkit.getScheduler()
        .runTaskAsynchronously(
            plugin,
            () -> {
              List<Double> probs =
                  plugin
                      .getDatabaseManager()
                      .getDatabase()
                      .getPlayerProbabilities(uuid, MAX_PROBABILITIES);
              if (!probs.isEmpty())
                Bukkit.getScheduler()
                    .runTask(
                        plugin,
                        () -> {
                          PlayerRiskData data = new PlayerRiskData(uuid, playerName);
                          for (Double prob : probs) data.addProbability(prob);
                          playerRisks.put(uuid, data);
                        });
            });
  }

  public void openMenu(Player viewer) {
    Inventory inventory = Bukkit.createInventory(null, MENU_SIZE, MENU_TITLE);
    java.util.List<PlayerRiskData> sortedPlayers =
        playerRisks.values().stream()
            .filter(data -> Bukkit.getPlayer(data.getUuid()) != null)
            .sorted(
                (p1, p2) -> Double.compare(p2.getAverageProbability(), p1.getAverageProbability()))
            .collect(Collectors.toList());
    int slot = 0;
    for (PlayerRiskData data : sortedPlayers) {
      if (slot >= MENU_SIZE) break;
      inventory.setItem(slot, WoolItemFactory.create(data));
      slot++;
    }
    viewer.openInventory(inventory);
  }

  public UUID getPlayerUuidBySlot(int slot) {
    if (slot < 0 || slot >= MENU_SIZE) return null;
    java.util.List<PlayerRiskData> sortedPlayers =
        playerRisks.values().stream()
            .filter(data -> Bukkit.getPlayer(data.getUuid()) != null)
            .sorted(
                (p1, p2) -> Double.compare(p2.getAverageProbability(), p1.getAverageProbability()))
            .collect(Collectors.toList());
    if (slot >= sortedPlayers.size()) return null;
    return sortedPlayers.get(slot).getUuid();
  }

  public void clear() {
    playerRisks.clear();
  }

  public static boolean isChickenCoopMenu(String title) {
    return MENU_TITLE.equals(title);
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/chickencoop/display/color/RiskColorResolver.java ---

package club.nezxenka.netvision.visual.menu.coop.display.color;

import org.bukkit.ChatColor;
import org.bukkit.Material;

public class RiskColorResolver {
  public ChatColor resolveChatColor(double probability) {
    if (probability > 0.9) return ChatColor.RED;
    if (probability > 0.5) return ChatColor.YELLOW;
    return ChatColor.GREEN;
  }

  public Material resolveWool(double probability) {
    if (probability > 0.9) return Material.RED_WOOL;
    if (probability > 0.5) return Material.YELLOW_WOOL;
    return Material.LIME_WOOL;
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/chickencoop/display/sorter/RiskSorter.java ---

package club.nezxenka.netvision.visual.menu.coop.display.sorter;

import club.nezxenka.netvision.visual.menu.coop.model.PlayerRiskData;
import java.util.Comparator;

public class RiskSorter implements Comparator<PlayerRiskData> {
  @Override
  public int compare(PlayerRiskData a, PlayerRiskData b) {
    return Double.compare(b.getAverageProbability(), a.getAverageProbability());
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/chickencoop/display/WoolItemFactory.java ---

package club.nezxenka.netvision.visual.menu.coop.display;

import club.nezxenka.netvision.visual.menu.coop.model.PlayerRiskData;
import java.util.ArrayList;
import java.util.List;
import org.bukkit.ChatColor;
import org.bukkit.Material;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;

public class WoolItemFactory {
  public static ItemStack create(PlayerRiskData data) {
    double avgProbability = data.getAverageProbability();
    Material woolType = getWoolByProbability(avgProbability);
    ItemStack wool = new ItemStack(woolType);
    ItemMeta meta = wool.getItemMeta();
    if (meta != null) {
      meta.setDisplayName(ChatColor.WHITE + data.getPlayerName());
      List<String> lore = new ArrayList<>();
      lore.add("");
      lore.add(ChatColor.GRAY + "Последние проверки:");
      List<Double> probs = data.getLastProbabilities();
      if (probs.isEmpty()) lore.add(ChatColor.GRAY + "Нет данных");
      else if (probs.size() <= 5) lore.add(formatLastProbabilitiesWithColors(probs));
      else {
        lore.add(formatLastProbabilitiesWithColors(probs.subList(0, 5)));
        lore.add(formatLastProbabilitiesWithColors(probs.subList(5, Math.min(10, probs.size()))));
      }
      lore.add("");
      lore.add(ChatColor.GRAY + "Средний риск:");
      ChatColor avgColor = getColorByProbability(avgProbability);
      lore.add(avgColor + "AVG " + String.format("%.2f", avgProbability));
      lore.add("");
      lore.add(ChatColor.GREEN + "Нажмите, чтобы следить");
      meta.setLore(lore);
      wool.setItemMeta(meta);
    }
    return wool;
  }

  private static Material getWoolByProbability(double probability) {
    if (probability > 0.9) return Material.RED_WOOL;
    else if (probability > 0.5) return Material.YELLOW_WOOL;
    else return Material.LIME_WOOL;
  }

  private static ChatColor getColorByProbability(double probability) {
    if (probability > 0.9) return ChatColor.RED;
    else if (probability > 0.5) return ChatColor.YELLOW;
    else return ChatColor.GREEN;
  }

  private static String formatLastProbabilitiesWithColors(List<Double> probabilities) {
    if (probabilities.isEmpty()) return ChatColor.GRAY + "Нет данных";
    StringBuilder result = new StringBuilder();
    for (int i = 0; i < probabilities.size(); i++) {
      double prob = probabilities.get(i);
      ChatColor color = getColorByProbability(prob);
      result.append(color).append(String.format("%.2f", prob));
      if (i < probabilities.size() - 1) result.append(ChatColor.GRAY).append(", ");
    }
    return result.toString();
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/chickencoop/model/cache/RiskDataCache.java ---

package club.nezxenka.netvision.visual.menu.coop.model.cache;

import club.nezxenka.netvision.visual.menu.coop.model.PlayerRiskData;
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.UUID;

public class RiskDataCache {

  private final LinkedHashMap<UUID, PlayerRiskData> cache;
  private final int maxSize;

  public RiskDataCache(int maxSize) {
    this.maxSize = maxSize;
    this.cache = new LinkedHashMap<>();
  }

  public void put(UUID uuid, PlayerRiskData data) {
    if (cache.size() >= maxSize) {
      var it = cache.keySet().iterator();
      if (it.hasNext()) {
        it.next();
        it.remove();
      }
    }
    cache.put(uuid, data);
  }

  public PlayerRiskData get(UUID uuid) {
    return cache.get(uuid);
  }

  public void remove(UUID uuid) {
    cache.remove(uuid);
  }

  public Map<UUID, PlayerRiskData> all() {
    return cache;
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/chickencoop/model/PlayerRiskData.java ---

package club.nezxenka.netvision.visual.menu.coop.model;

import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Deque;
import java.util.List;
import java.util.UUID;

public class PlayerRiskData {

  private final UUID uuid;
  private final String playerName;
  private final Deque<Double> probabilities = new ArrayDeque<>();
  private static final int MAX_PROBABILITIES = 10;

  public PlayerRiskData(UUID uuid, String playerName) {
    this.uuid = uuid;
    this.playerName = playerName;
  }

  public void addProbability(double probability) {
    probabilities.addLast(probability);
    if (probabilities.size() > MAX_PROBABILITIES) probabilities.removeFirst();
  }

  public List<Double> getLastProbabilities() {
    List<Double> reversed = new ArrayList<>(probabilities);
    Collections.reverse(reversed);
    return reversed;
  }

  public double getAverageProbability() {
    if (probabilities.isEmpty()) return 0.0;
    return probabilities.stream().mapToDouble(Double::doubleValue).average().orElse(0.0);
  }

  public UUID getUuid() {
    return uuid;
  }

  public String getPlayerName() {
    return playerName;
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/display/color/HistoryColorScheme.java ---

package club.nezxenka.netvision.visual.menu.history.display.color;

import org.bukkit.ChatColor;
import org.bukkit.Material;

public class HistoryColorScheme {
  public ChatColor textColor(double probability) {
    if (probability > 0.9) return ChatColor.RED;
    if (probability > 0.5) return ChatColor.YELLOW;
    return ChatColor.GREEN;
  }

  public Material glassType(double probability) {
    if (probability > 0.9) return Material.RED_STAINED_GLASS_PANE;
    if (probability > 0.5) return Material.YELLOW_STAINED_GLASS_PANE;
    return Material.LIME_STAINED_GLASS_PANE;
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/display/HistoryNavigationRenderer.java ---

package club.nezxenka.netvision.visual.menu.history.display;

import org.bukkit.ChatColor;
import org.bukkit.Material;
import org.bukkit.inventory.Inventory;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;

public class HistoryNavigationRenderer {
  private static final String PREV_ARROW_NAME = ChatColor.GREEN + "← Предыдущая страница";
  private static final String NEXT_ARROW_NAME = ChatColor.GREEN + "Следующая страница →";

  public static void fillNavigation(Inventory inv, int currentPage, int totalPages) {
    if (currentPage == 1) {
      for (int i = 45; i <= 52; i++) inv.setItem(i, createGlassPane());
      inv.setItem(53, totalPages > 1 ? createArrowItem(false) : createGlassPane());
    } else if (currentPage == totalPages) {
      inv.setItem(45, createArrowItem(true));
      for (int i = 46; i <= 53; i++) inv.setItem(i, createGlassPane());
    } else {
      inv.setItem(45, createArrowItem(true));
      for (int i = 46; i <= 52; i++) inv.setItem(i, createGlassPane());
      inv.setItem(53, createArrowItem(false));
    }
  }

  private static ItemStack createGlassPane() {
    ItemStack pane = new ItemStack(Material.GRAY_STAINED_GLASS_PANE);
    ItemMeta meta = pane.getItemMeta();
    if (meta != null) {
      meta.setDisplayName(" ");
      pane.setItemMeta(meta);
    }
    return pane;
  }

  private static ItemStack createArrowItem(boolean previous) {
    ItemStack item = new ItemStack(Material.ARROW);
    ItemMeta meta = item.getItemMeta();
    if (meta != null) {
      meta.setDisplayName(previous ? PREV_ARROW_NAME : NEXT_ARROW_NAME);
      item.setItemMeta(meta);
    }
    return item;
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/display/HistoryPaneFactory.java ---

package club.nezxenka.netvision.visual.menu.history.display;

import club.nezxenka.netvision.core.storage.model.ProbabilityEntry;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;
import org.bukkit.ChatColor;
import org.bukkit.Material;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;

public class HistoryPaneFactory {
  public static ItemStack create(List<ProbabilityEntry> entries) {
    double avg = entries.stream().mapToDouble(ProbabilityEntry::probability).average().orElse(0.0);
    ChatColor avgColor = getColorByProbability(avg);
    ItemStack glass = new ItemStack(getGlassByProbability(avg));
    ItemMeta meta = glass.getItemMeta();
    if (meta != null) {
      meta.setDisplayName(avgColor + "AVG: " + String.format("%.4f", avg));
      List<String> lore = new ArrayList<>();
      lore.add("");
      lore.add(
          ChatColor.WHITE
              + "Вер. "
              + ChatColor.DARK_GRAY
              + "   |"
              + ChatColor.WHITE
              + " Сервер"
              + ChatColor.DARK_GRAY
              + "    |"
              + ChatColor.WHITE
              + " Время");
      lore.add(ChatColor.DARK_GRAY + "──────────────────");
      for (ProbabilityEntry entry : entries) {
        ChatColor probColor = getColorByProbability(entry.probability());
        long elapsed = System.currentTimeMillis() - entry.createdAt();
        String timeStr = formatElapsed(elapsed);
        String serverName = entry.server();
        lore.add(
            probColor
                + String.format("%.4f", entry.probability())
                + " "
                + ChatColor.GRAY
                + "|"
                + ChatColor.GRAY
                + " "
                + ChatColor.GRAY
                + serverName
                + " "
                + ChatColor.GRAY
                + "|"
                + ChatColor.GRAY
                + " "
                + timeStr);
      }
      meta.setLore(lore);
      glass.setItemMeta(meta);
    }
    return glass;
  }

  private static Material getGlassByProbability(double probability) {
    if (probability > 0.9) return Material.RED_STAINED_GLASS_PANE;
    if (probability > 0.5) return Material.YELLOW_STAINED_GLASS_PANE;
    return Material.LIME_STAINED_GLASS_PANE;
  }

  private static ChatColor getColorByProbability(double probability) {
    if (probability > 0.9) return ChatColor.RED;
    if (probability > 0.5) return ChatColor.YELLOW;
    return ChatColor.GREEN;
  }

  private static String formatElapsed(long millis) {
    if (millis < 0) return "0ч. 0м.";
    long hours = TimeUnit.MILLISECONDS.toHours(millis);
    long minutes = TimeUnit.MILLISECONDS.toMinutes(millis) % 60;
    return hours + "ч. " + minutes + "м.";
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/display/pagination/PageCalculator.java ---

package club.nezxenka.netvision.visual.menu.history.display.pagination;

public class PageCalculator {
  private final int entriesPerPage;
  private final int maxPages;

  public PageCalculator(int entriesPerPage, int maxPages) {
    this.entriesPerPage = entriesPerPage;
    this.maxPages = maxPages;
  }

  public int computeTotalPages(int totalEntries) {
    return Math.max(1, Math.min((int) Math.ceil((double) totalEntries / entriesPerPage), maxPages));
  }

  public int clampPage(int page, int totalPages) {
    if (page < 1) return 1;
    if (page > totalPages) return totalPages;
    return page;
  }

  public int startIndex(int page) {
    return (page - 1) * entriesPerPage;
  }

  public int endIndex(int page, int total) {
    return Math.min(startIndex(page) + entriesPerPage, total);
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/HistoryMenu.java ---

package club.nezxenka.netvision.visual.menu.history;

import club.nezxenka.netvision.NetVision;
import club.nezxenka.netvision.core.storage.api.RecordStorage;
import club.nezxenka.netvision.core.storage.model.ProbabilityEntry;
import club.nezxenka.netvision.visual.menu.history.display.HistoryNavigationRenderer;
import club.nezxenka.netvision.visual.menu.history.display.HistoryPaneFactory;
import club.nezxenka.netvision.visual.menu.history.model.HistorySession;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import org.bukkit.Bukkit;
import org.bukkit.ChatColor;
import org.bukkit.entity.Player;
import org.bukkit.inventory.Inventory;

public class HistoryMenu {
  private static final int MENU_SIZE = 54;
  private static final int CONTENT_SLOTS = 45;
  private static final int ENTRIES_PER_PAGE = CONTENT_SLOTS;
  private static final int PROBS_PER_ENTRY = 10;
  private static final int MAX_PAGES = 3;
  private static final String MENU_PREFIX = ChatColor.DARK_GRAY + "История ";
  private final NetVision plugin;
  private final Map<UUID, HistorySession> activeSessions = new ConcurrentHashMap<>();

  public HistoryMenu(NetVision plugin) {
    this.plugin = plugin;
  }

  public void open(Player viewer, String targetName, UUID targetUuid, int page) {
    RecordStorage db = plugin.getDatabaseManager().getDatabase();
    Bukkit.getScheduler()
        .runTaskAsynchronously(
            plugin,
            () -> {
              int totalProbs = db.getPlayerProbabilityCount(targetUuid);
              int maxEntries = MAX_PAGES * ENTRIES_PER_PAGE;
              int maxProbs = maxEntries * PROBS_PER_ENTRY;
              int probsToLoad = Math.min(totalProbs, maxProbs);
              List<List<ProbabilityEntry>> batches = new ArrayList<>();
              int loaded = 0;
              while (loaded < probsToLoad) {
                List<ProbabilityEntry> entries =
                    db.getPlayerProbabilityEntries(targetUuid, PROBS_PER_ENTRY, loaded);
                if (entries.isEmpty()) break;
                batches.add(entries);
                loaded += entries.size();
                if (entries.size() < PROBS_PER_ENTRY) break;
              }
              int computedTotalPages =
                  Math.max(1, (int) Math.ceil((double) batches.size() / ENTRIES_PER_PAGE));
              if (computedTotalPages > MAX_PAGES) computedTotalPages = MAX_PAGES;
              int clampedPage = page;
              if (clampedPage < 1) clampedPage = 1;
              if (clampedPage > computedTotalPages) clampedPage = computedTotalPages;
              final int currentPage = clampedPage;
              final int finalTotalPages = computedTotalPages;
              Bukkit.getScheduler()
                  .runTask(
                      plugin,
                      () -> {
                        activeSessions.put(
                            viewer.getUniqueId(),
                            new HistorySession(targetUuid, targetName, currentPage));
                        Inventory inv =
                            createInventory(targetName, currentPage, finalTotalPages, batches);
                        viewer.openInventory(inv);
                      });
            });
  }

  private Inventory createInventory(
      String targetName, int currentPage, int totalPages, List<List<ProbabilityEntry>> allBatches) {
    String title = MENU_PREFIX + ChatColor.DARK_GRAY + targetName;
    Inventory inv = Bukkit.createInventory(null, MENU_SIZE, title);
    int startIdx = (currentPage - 1) * ENTRIES_PER_PAGE;
    int endIdx = Math.min(startIdx + ENTRIES_PER_PAGE, allBatches.size());
    int slot = 0;
    for (int i = startIdx; i < endIdx; i++) {
      inv.setItem(slot, HistoryPaneFactory.create(allBatches.get(i)));
      slot++;
    }
    HistoryNavigationRenderer.fillNavigation(inv, currentPage, totalPages);
    return inv;
  }

  public boolean handleClick(Player viewer, int rawSlot) {
    HistorySession session = activeSessions.get(viewer.getUniqueId());
    if (session == null) return false;
    if (rawSlot < 0 || rawSlot >= MENU_SIZE) return false;
    if (rawSlot >= 45 && rawSlot <= 53) {
      int newPage = session.currentPage();
      if (rawSlot == 45) {
        if (session.currentPage() > 1) newPage = session.currentPage() - 1;
      } else if (rawSlot == 53) newPage = session.currentPage() + 1;
      if (newPage != session.currentPage()) {
        viewer.closeInventory();
        open(viewer, session.targetName(), session.targetUuid(), newPage);
        return true;
      }
    }
    return false;
  }

  public void removeSession(UUID viewerUuid) {
    activeSessions.remove(viewerUuid);
  }

  public static boolean isHistoryMenu(String title) {
    return title != null && title.startsWith(MENU_PREFIX);
  }
}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/model/HistorySession.java ---

package club.nezxenka.netvision.visual.menu.history.model;

import java.util.UUID;

public record HistorySession(UUID targetUuid, String targetName, int currentPage) {}


--- src/main/java/club/nezxenka/netvision/visual/menu/history/model/page/PageState.java ---

package club.nezxenka.netvision.visual.menu.history.model.page;

import java.util.UUID;

public record PageState(UUID targetUuid, String targetName, int currentPage, int totalPages) {}


--- src/main/resources/config.yml ---

# NetVision мейн конфиг
# Локализация: en, ru
locale: "ru"

ai:
  # Включить AI Check?
  enabled: false
  # Ссылка на API для AI.
  server: "https://api.server.com/"
  # Тут API ключ для AI сервера.
  api-key: "NetVision"
  # Количество тиков, которые надо отправить в последовательности AI.
  sequence: 40
  #Количество тиков, которое нужно подождать перед отправкой следующей последовательности.
  step: 10
  buffer:
    # Уровень нарушения (VL), при котором будет флаг.
    flag: 50.0
    # Значение, до которого сбрасывается буфер после флага.
    reset-on-flag: 25.0
    # Множитель для увеличения буфера при высокой вероятности читерства (>0,9).
    # (probability - 0.9) * multiplier
    multiplier: 100.0
    # Сумма, вычитаемая из буфера при низкой вероятности читерства (<0,1).
    decrease: 0.25
  damage-reduction:
    # Включить урезание урона?
    enabled: true
    # Вероятность для начала урезания.
    prob: 0.9
    # Множители дамага
    # 0.1: с вероятностью 100% (1.0) урон будет снижен на 10%.
    # 1.0: с вероятностью 100% (1.0) урон будет снижен на 100%.
    # 2.0: с вероятностью 95% (0,95) урон будет снижен на 100%.
    multiplier: 1.0
  collect-mode:
    # Включить режим сбора данных для обучения нейросети.
    # Вместо отправки на AI-сервер, тики сохраняются в папку plugins/NetVision/collected/<label>/
    enabled: false
    # Метка: legit (честная игра) или cheat (с читами).
    # Переключи и используй /nvз reload, чтобы сменить папку сохранения.
    label: "legit"
  worldguard:
    # Включить проверку региона WorldGuard?
    enabled: true
    # Список регионов WorldGuard, где проверка ИИ отключена.
    disabled-regions:
      - "spawn:spawn"

client-brand:
  # Это означает, что версия не будет отображаться админам, если она соответствует следующим версиям.
  ignored-clients:
    - "^vanilla$"
    - "^fabric$"
    - "^lunarclient:v\\d+\\.\\d+\\.\\d+-\\d{4}$"
    - "^Feather Fabric$"
    - "^labymod$"
  # NetVision внесет в черный список определенные версии Forge, содержащие встроенные читы для Reach (Forge 1.18.2–1.19.3).
  # Установка этого параметра в значение false позволит указанным клиентам подключаться к серверу. Отключайте эту функцию на свой страх и риск.
  disconnect-blacklisted-forge-versions: true

alerts:
  print-to-console: true

history:
  enabled: true

suspicious:
  alerts:
    buffer: 25.0

redis:
  # Включить Redis соединение. Требуется для cross-server оповещений.
  enabled: false
  host: "localhost"
  port: 6379
  # Оставь пустым, если у Redis нет пароля.
  password: ""
  # Логическая БД.
  database: 0
  # Подключение через TLS (rediss://).
  ssl: false
  # Таймаут подключения и команд (сек).
  timeout-seconds: 10

# Транслировать оповещения между серверами, использующими один Redis.
cross-server:
  # Требует redis.enabled выше.
  enabled: false
  # Имя, которое будет отображаться как тег источника, например [Lobby].
  server-name: "server-1"
  # Redis pub/sub канал. Все серверы должны использовать одно и то же значение.
  channel: "netvision:alerts"
  # Какие типы оповещений транслировать.
  alerts:
    # Нарушения (флаги).
    regular: true
    # Подозрительные игроки (буфер). Включение также запускает синхронизацию
    # списка подозрительных игроков между серверами.
    suspicious: true
  # Настройка синхронизации списка подозрительных (используется только если cross-server.alerts.suspicious включён).
  suspicious-sync:
    # Как долго запись живёт в Redis без обновления (сек).
    ttl-seconds: 30
    # Как часто сервер публикует своих подозрительных игроков (сек).
    refresh-seconds: 10

exemptions:
  # Исключить Bedrock-игроков (Geyser/Floodgate) из всех проверок?
  bedrock: true

debug:
  # AI_PROBABILITY, AI_TIMEOUT, WORLDGUARD, PACKET_DUPLICATION
  enabled-categories: []


--- src/main/resources/messages/messages_en.yml ---

# \u00BB is » (double >>), ANSI and UTF-8 interpret this differently... you may even see ? due to this
prefix: "<blue>NetVision <dark_gray>\u00BB"

alerts-enabled: "<prefix> <white>Alerts enabled."
alerts-disabled: "<prefix> <white>Alerts disabled."
alerts-format: "<prefix> <white><player> <red>failed</red> <white><check_name></white> (x<red><vl></red>) <gray><verbose>"

player-not-found: "<prefix> <red>Player is exempt or offline!"
run-as-player: "<prefix> <red>This command can only be used by players!"
reload-start: "<prefix> <yellow>Reloading NetVision configuration..."
reload-success: "<prefix> <green>NetVision configuration reloaded."

prob:
  enabled: "<green>Probability display for <player> enabled."
  disabled: "<yellow>Probability display for <player> disabled."
  no-data: "<red>Could not get data for <player>."
  no-aicheck: "<red>AI-Check is not active for <player>."
  format:
    label-prob: "Prob"
    label-buffer: "Buffer"
    label-ping: "Ping"
    separator: " | "
    suffix-ping: "ms"

brand:
  alerts-enabled: "<prefix> <white>Brand notifications enabled."
  alerts-disabled: "<prefix> <white>Brand notifications disabled."
  notification: "<prefix> <white><player></white> <gray>joined using <red><brand></red>"
  disconnect-forge: "<red>Your Forge version is blacklisted due to a built-in reach exploit.\n<yellow>Affected versions: 1.18.2-1.19.3\n<gray>Please update Forge or use a different client."

profile:
  no-data: "<prefix> <red>Could not get data (exempt or offline)."
  lines:
    - "<gray>======================"
    - "<prefix> <red>Profile for <white><player>"
    - "<red>Ping: <white><ping>"
    - "<red>Version: <white><version>"
    - "<red>Client Brand: <white><brand>"
    - "<red>Session Time: <white><session_time>"
    - "<red>Total Playtime: <white><total_playtime>"
    - "<red>Horizontal Sensitivity: <white><sens_x>"
    - "<red>Vertical Sensitivity: <white><sens_y>"
    - "<red>AI Buffer: <white><ai_buffer>"
    - "<red>Predictions >0.9: <white><ai_probs_90>"
    - "<gray>======================"

history:
  disabled: "<prefix> <red>History subsystem is disabled!"
  header: "<prefix> <red>Showing logs for <white><player></white> (<white><page></white>/<white><max_pages></white>)"
  entry: "<prefix> <dark_gray>[<white><server></white>] <red>Failed <check> (x<red><vl></red>) <gray><verbose></gray> (<red><timeago></red>)"
  no-violations: "<prefix> <gray>No violations found for this player."

logs:
  header: "<prefix> <red>Showing all logs (<white><page></white>/<white><max_pages></white>)"
  entry: "<prefix> <dark_gray>[<white><server></white>] <white><player></white> <red>failed <check> (x<red><vl></red>) <gray><verbose></gray> (<red><timeago></red>)"
  no-violations: "<prefix> <gray>No violations found."
  invalid-time: "<prefix> <red>Invalid time format. Use like --time 15m/15h/15d."

punish:
  reset-success: "<prefix> <green>All violation levels (VL) for player <player> have been reset."

suspicious:
  alerts-enabled: "<prefix> <green>Suspicious player alerts enabled."
  alerts-disabled: "<prefix> <yellow>Suspicious player alerts disabled."
  alert-triggered: "<prefix> Player <white><player></white> is suspicious (Buffer: <red><buffer></red>)."
  list-empty: "<prefix> <gray>No suspicious players online."
  list-header: "<prefix> <gray>List of suspicious players (<red><count></red>):"
  list-entry: " <gray>-</gray> <white><player></white> <dark_gray>| <gray>Buffer: <red><buffer></red>, Ping: <red><ping>ms</red>"
  top-none: "<prefix> <gray>No highly suspicious players at the moment."
  top-player: "<prefix> <gray>Top suspicious: <red><player></red> (Buffer: <red><buffer></red>)."

stats:
  lines:
    - "<gray>================== <blue>NetVision <red>Statistics</red> <gray>=================="
    - "<red>Total Flags (24h): <white><flags_24h>"
    - "<red>Unique Violators (24h): <white><violators_24h>"
    - "<red>Players Online: <white><online_players>"
    - "<red>Currently Suspicious: <white><suspicious_now>"
    - "<gray>==============================================="

help:
  - "<gray>================== <blue>NetVision <red>Help</red> <gray>=================="
  - "<red>/<command> alerts</red> <gray>-</gray> <white>Toggle violation alerts."
  - "<red>/<command> brands</red> <gray>-</gray> <white>Toggle client brand notifications."
  - "<red>/<command> profile <player></red> <gray>-</gray> <white>View a player's profile."
  - "<red>/<command> prob <player></red> <gray>-</gray> <white>Live view AI probabilities for a player."
  - "<red>/<command> reload</red> <gray>-</gray> <white>Reload the configuration."
  - "<red>/<command> history <player> [page]</red> <gray>-</gray> <white>View a player's violation history."
  - "<red>/<command> menu</red> <gray>-</gray> <white>Open kyriatnik."
  - "<red>/<command> status</red> <gray>-</gray> <white>Enable hologram on players."
  - "<red>/<command> punish reset <player></red> <gray>-</gray> <white>Reset all of a player's violation levels."
  - "<red>/<command> help</red> <gray>-</gray> <white>View this help message."
  - "<gray>==============================================="

internal:
  error: "<red>An error occurred whilst processing packets."

time:
  ago: " ago"
  days: "d"
  hours: "h"
  minutes: "m"
  seconds: "s"


--- src/main/resources/messages/messages_ru.yml ---

# \u00BB это " (двойной >>), ANSI и UTF-8 интерпретируют это по-разному... вы можете даже увидеть "?" из-за этого
prefix: "<blue>NetVision <dark_gray>\u00BB"

alerts-enabled: "<prefix> <white>Оповещения включены."
alerts-disabled: "<prefix> <white>Оповещения выключены."
alerts-format: "<prefix> <white><player> <red>провалил</red> <white><check_name></white> (x<red><vl></red>) <gray><verbose>"

player-not-found: "<prefix> <red>Игрок исключен или находится вне сети!"
run-as-player: "<prefix> <red>Эту команду могут использовать только игроки!"
reload-start: "<prefix> <yellow>Перезагрузка конфигурации NetVision..."
reload-success: "<prefix> <green>Конфигурация NetVision перезагружена."

prob:
  enabled: "<green>Отображение вероятностей для <player> включено."
  disabled: "<yellow>Отображение вероятностей для <player> выключено."
  no-data: "<red>Не удалось получить данные для <player>."
  no-aicheck: "<red>AI-Чек неактивен для <player>."
  format:
    label-prob: "Вер-ть"
    label-buffer: "Буфер"
    label-ping: "Пинг"
    separator: " | "
    suffix-ping: "мс"

brand:
  alerts-enabled: "<prefix> <white>Оповещения о клиентах включены."
  alerts-disabled: "<prefix> <white>Оповещения о клиентах выключены."
  notification: "<prefix> <white><player></white> <gray>присоединился, используя <red><brand></red>"
  disconnect-forge: "<red>Ваша версия Forge в черном списке из-за встроенного эксплоита на дистанцию атаки.\n<yellow>Затронутые версии: 1.18.2-1.19.3\n<gray>Пожалуйста, обновите Forge или используйте другой клиент."

profile:
  no-data: "<prefix> <red>Не удалось получить данные (исключён или оффлайн)."
  lines:
    - "<gray>======================"
    - "<prefix> <red>Профиль игрока <white><player>"
    - "<red>Пинг: <white><ping>"
    - "<red>Версия: <white><version>"
    - "<red>Клиент: <white><brand>"
    - "<red>Время сессии: <white><session_time>"
    - "<red>Общее время игры: <white><total_playtime>"
    - "<red>Горизонтальная чувствительность: <white><sens_x>"
    - "<red>Вертикальная чувствительность: <white><sens_y>"
    - "<red>AI буфер: <white><ai_buffer>"
    - "<red>Предсказаний >0.9: <white><ai_probs_90>"
    - "<gray>======================"

history:
  disabled: "<prefix> <red>Подсистема истории отключена!"
  header: "<prefix> <red>Показ журналов для <white><player></white> (<white><page></white>/<white><max_pages></white>)"
  entry: "<prefix> <dark_gray>[<white><server></white>] <red>Провалено <check> (x<red><vl></red>) <gray><verbose></gray> (<red><timeago></red>)"
  no-violations: "<prefix> <gray>Для этого игрока не найдено нарушений."

logs:
  header: "<prefix> <red>Показаны все логи (<white><page></white>/<white><max_pages></white>)"
  entry: "<prefix> <dark_gray>[<white><server></white>] <white><player></white> <red>провалил <check> (x<red><vl></red>) <gray><verbose></gray> (<red><timeago></red>)"
  no-violations: "<prefix> <gray>Нарушения не найдены."
  invalid-time: "<prefix> <red>Неверный формат времени. Используйте --time 15m/15h/15d."

punish:
  reset-success: "<prefix> <green>Все уровни нарушений (VL) для игрока <player> были сброшены."

suspicious:
  alerts-enabled: "<prefix> <green>Оповещения о подозрительных игроках включены."
  alerts-disabled: "<prefix> <yellow>Оповещения о подозрительных игроках выключены."
  alert-triggered: "<prefix> Игрок <white><player></white> подозрителен (Буфер: <red><buffer></red>)."
  list-empty: "<prefix> <gray>Подозрительных игроков онлайн нет."
  list-header: "<prefix> <gray>Список подозрительных игроков (<red><count></red>):"
  list-entry: " <gray>-</gray> <white><player></white> <dark_gray>| <gray>Буфер: <red><buffer></red>, Пинг: <red><ping>ms</red>"
  top-none: "<prefix> <gray>В данный момент нет явно подозрительных игроков."
  top-player: "<prefix> <gray>Самый подозрительный: <red><player></red> (Буфер: <red><buffer></red>)."

stats:
  lines:
    - "<gray>================== <blue>NetVision <red>Статистика</red> <gray>=================="
    - "<red>Всего флагов (24ч): <white><flags_24h>"
    - "<red>Уникальных нарушителей (24ч): <white><violators_24h>"
    - "<red>Игроков онлайн: <white><online_players>"
    - "<red>Сейчас подозрительных: <white><suspicious_now>"
    - "<gray>==============================================="

help:
  - "<gray>================== <blue>NetVision <red>помощь</red> <gray>=================="
  - "<red>/<command> alerts</red> <gray>-</gray> <white>Включить/выключить оповещения о нарушениях."
  - "<red>/<command> brands</red> <gray>-</gray> <white>Включить/выключить оповещения о клиенте игрока."
  - "<red>/<command> profile <player></red> <gray>-</gray> <white>Просмотреть профиль игрока."
  - "<red>/<command> prob <player></red> <gray>-</gray> <white>Просмотр вероятностей ИИ для игрока в реальном времени."
  - "<red>/<command> reload</red> <gray>-</gray> <white>Перезагрузить конфигурацию."
  - "<red>/<command> history <player> [page]</red> <gray>-</gray> <white>Просмотреть историю нарушений игрока."
  - "<red>/<command> menu</red> <gray>-</gray> <white>Открывает курятник."
  - "<red>/<command> status</red> <gray>-</gray> <white>Включает голограмму над игроками."
  - "<red>/<command> punish reset <player></red> <gray>-</gray> <white>Сбросить все уровни нарушений игрока."
  - "<red>/<command> help</red> <gray>-</gray> <white>Показать это справочное сообщение."
  - "<gray>==============================================="

internal:
  error: "<red>Произошла ошибка при обработке пакетов."

time:
  ago: " назад"
  days: "д"
  hours: "ч"
  minutes: "м"
  seconds: "с"


--- src/main/resources/punishments.yml ---

# <check_name> - название чека
# <vl> - нарушения
# <verbose> - дополнительная информация
# <player> - ник игрока
# [alert] - алерт
# [broadcast] отправка сообщения всем игрокам на сервере
# [reset] - ресет нарушений для игрока

Punishments:
  AI:
    checks:
      - "AI (Aim)"
    actions:
      1:
        - "[alert]"
        - "[log]"
        - "shame ban <player>"
      3:
        - "[alert]"
        - "[log]"
        - "shame ban <player>"
      5:
        - "[alert]"
        - "[log]"
        - "shame ban <player>"
      7:
        - "[alert]"
        - "[log]"
        - "shame ban <player>"
      9:
        - "[alert]"
        - "[log]"
        - "shame ban <player>"
      15:
        - "[alert]"
        - "[log]"
        - "shame ban <player>"
        - "[reset]"

